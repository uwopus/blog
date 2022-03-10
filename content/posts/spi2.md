---
title: "Main Board and Pico Communications Revisit"
date: 2022-02-24T20:15:51-05:00
draft: false
---

February 24th, 2022

Today, we found some major issues with the way that the communications was implemented last month. 

## Issue
Prior to these changes, the high level communications protocol was as follows: 
- Zero sends a command to the Pico
- Pico recieves commands, sends back an ACK/NACK along with any data requested

This scheme was highly dependent on the states being synchronized between the Pico and the Zero. If a message was missed, both the Pico and Zero would be expecting to receive data. This was verified by using a logic analyzer to view the activity on the bus. When this happened, communications would immediately stall between the Pico and Zero and not recover until another fluke caused the communications to re-establish. 

## Solution Attempt 1: CRC Reset
The first attempt to solve this problem was to simply implement some kind of error checking on the recieved messages. If the checksum failed, then the Pico would reset to recieve mode until it recieved a valid message. However, there was no way to tell the Pico that the checksum failed as there were not free GPIO pins to establish a side-band communication. 

## Solution Attempt 2: Multicore and Communication Protocol Change
This attempt involved exploiting the second core of the Raspberry Pi Pico. The control code was moved completely to the second core while the first core exclusively handled communications. Then, we were able to re-design the communication protocol to be much simpler. Instead of a call-and-response methodology where the Zero would request data from the Pico, we created a scheme where all relevant information is sent at a regular interval. 

The Zero would send packets that look like this: 

```c
typedef struct opus_pico_tx_packet {
    uint32_t t_ms; // sequence number, monotonically increasing until wraparound 
    int32_t L_encd_ticks;
    float L_cur_vel;
    float L_goal_vel;
    int32_t R_encd_ticks;
    float R_cur_vel;
    float R_goal_vel;
    struct {
        uint8_t state;
        uint8_t pad1;
        uint8_t pad2;
        uint8_t crc;
    } state_pad_pad_crc;
} opus_pico_tx_packet_t;
```

This means that the zero would regularly get an update on the full state of the pico, including the calculated velocities of the motors, the current ticks, and the state. The `state_pad_pad_crc` struct was required to word-align the overall structure. 

The Pico would send packets that look like this: 

```c
typedef struct opus_pico_rx_packet {
    uint32_t t_ms; // sequence number, monotonically increasing until wraparound 
    float L_vel_cmd;
    float R_vel_cmd;
    uint8_t state_cmd;
    uint8_t state_pad[3];
    uint8_t reserved[15];
    uint8_t crc;
} opus_pico_rx_packet_t;
```

CRC was added so that malformed packets can be rejected. This solution worked very well! The Zero sends commands every 5ms, while the Pico sends state information back every 10ms. This asymmetry is a quirk of the way this was implemented in the code. 