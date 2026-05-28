<!--
Copyright (C) 2026 Sarva & Pratik: Coding Club, IIT Dharwad - All Rights Reserved
You may use, distribute and modify this code under the
terms of the MIT license.

You should have received a copy of the MIT license with
this file. If not, please write to: codingclub@iitdh.ac.in, or visit 
https://mit-license.org/
-->

2.  Communication

    1. Creating a protocol that will be used to send and receive packets of
    instructions, data and metadata.

        a. Design a data link layer agnostic protocol. Choose a suitable
        statefulness for the protocol (connection based or not). Explain at a
        high level how the handshaking mechanism will work if it is connection
        based.
            - The protocol uses a connection-oriented stateful architecture
            where communication occurs through logical sessions identified using
            SESSION_ID values.
            - Sessions allow the host and embedded devices to maintain runtime
            state, execution tracking, retransmission queues, and active
            workloads.
            - Communication begins using a three-way handshake:
                1. Host sends CONNECT packet.
                2. Device validates compatibility and responds with CONNECT_ACK
                or CONNECT_NACK.
                3. Host sends final ACK to establish the session.

            - Reliable communication is implemented using sliding window ARQ,
            allowing multiple packets to remain in-flight simultaneously.
            - Sequence numbers track packet ordering and retransmissions.

            - The protocol uses KEEPALIVE packets for liveness monitoring.
            - If heartbeat packets are not received within the timeout period,
            the session enters ERROR state.
<!--        TODO: Specify the time period for KEEPALIVE and retransmissions -->

            - Flow control is implemented using RX_WINDOW so receivers can
            advertise available packet capacity and prevent buffer overflow.

            - Error recovery uses deterministic recovery logic:
                - session enters ERROR state
                - queues flushed
                - RESET packet transmitted
                - handshake restarted
                - session re-established
<!--        TODO: Create state machines for this protocol for both sender and
            receiver. The state machines should encode liveness monitoring,
            flow control states and error recovery -->

        b. Design a extensible but efficient packet structure that can
        encapsulate details related to instruction batches, data batches and any
        other protocol specific interactions.
            - The protocol uses a packet-based communication system with:
                - fixed-size header
                - variable-size payload

            - Packet header fields:
                - MAGIC
                - VER
<!--            TODO: What does this field do? -->
                - TYPE
                - FLAGS
                - SOURCE_DEVICE
                - DEST_DEVICE
                - SESSION_ID
                - SEQUENCE_NUM
                - RX_WINDOW
                - PAYLOAD_LEN
                - CRC-16

            - Payloads transport:
                - instructions
                - tensors
                - runtime queries (device info)
                - responses
                - timestamp
<!--        TODO: Remove above! Protocol should not specify payload content -->

            - FLAGS support:
                - acknowledgements
                - fragmentation
                - compression
<!--            TODO: Undefined!! How does compression work here? -->

            - Large payloads are fragmented using:
                - FRAGMENT_ID
                - TOTAL_FRAGMENTS

            - The protocol supports extensibility using:
                - reserved packet types
                - protocol versioning
<!--        TODO: Undefined!! And also never asked for in requirements -->

        c. Mention bit width and valid values for each field.
            - MAGIC
                - Width: 16 bits
                - Value: 0xEB57

            - VER
                - Width: 4 bits
                - Values: 0x1–0xE

            - TYPE
                - Width: 4 bits
                - Values: 0x0–0xF
<!--        TODO: What does the TYPE field do? -->

            - FLAGS
                - Width: 8 bits
                - Bits:
                    - ACK
                    - FRAG
                    - LAST_FRAG
                    - COMPRESSED
                    - RESERVED (4 bits)

            - SESSION_ID
                - Width: 16 bits

            - SEQUENCE_NUM
                - Width: 16 bits

            - RX_WINDOW
                - Width: 8 bits

            - PAYLOAD_LEN
                - Width: 16 bits

            - CRC-16
                - Width: 16 bits
                - CRC-16/CCITT
                - polynomial: 0x1021
                - initial value: 0xFFFF
<!--        TODO: Why CRC? Is it not taken care of by data link layer? -->

            - Serialization rules:
                - all integers use big-endian format
                - packed byte layout
                - no implicit padding
                - UTF-8 strings
<!--            TODO: Keep ASCII ONLY for commands, payload is bytes. UTF-8 is
                not necessary in the protocol.
                Let the receiver handle the payload -->

        d. Program sender and receiver functions for the host and various
        devices that can parse the packets efficiently.
            - Host sender flow:
                1. create packet
                2. fill header fields
                3. serialize payload
                4. compute CRC
<!--            TODO: CRC field comes in the header. How do you compute this
                without CRC itself being present there?
                Look into pseudo fields in IPv4 and how checksums are computed
                to find your answer -->
                5. queue packet
                6. transmit packet
                7. start retransmission timer
                8. wait for ACK
                9. retransmit on timeout

            - Receiver parsing flow:
                1. scan for MAGIC
                2. parse header
                3. validate payload length
                4. read payload
                5. compute CRC
<!--            TODO: Same problem as above.
                Is CRC computed with the CRC field inside it or not? -->
                6. validate SESSION_ID
                7. check sequence number
                8. handle fragmentation
                9. dispatch payload to subsystem
<!--    TODO: What about routing via multiple devices rather than direct
        connections? -->

    2. Creating an Intermediate Representation (IR) that can capture semantics
    of instructions and data development boards
<!--    TODO: Specify bitwise structure of opcode and data packing. -->

        a. Create instructions for querying device information.
            - 0x01    GET_DEVICE_INFO
<!--        TODO: Does the above not cover RAM and flash as well? -->
            - 0x02    GET_RAM_INFO
            - 0x03    GET_FLASH_INFO
            - 0x05    GET_DEVICE_NAME
            - 0x06    GET_SUPPORTED_PROT_VERSIONS
<!--    TODO: Define an ERROR op. - NOT THE SAME ONE AS IN THE PROTOCOL -->
            - unsupported capability instructions should return ERROR packets
<!--    TODO: Why is GET_DEVICE_INFO separate from GET_CAPABILITIES? -->
            - capability availability should be verified using GET_CAPABILITIES
            before execution

        b. Create instructions for input/output between boards.
            - 0x10    SEND_DATA
            - 0x11    RECV_DATA

            TODO: Remove
            - 0x12    STREAM_START
            - 0x13    STREAM_CONTINUE
            - 0x14    STREAM_END

        c. Create instructions for each capability supported in the boards.
            - 0x20    WIFI_CONN   Connect to wifi
            - 0x21    WIFI_SEND   Send wifi data
<!--        TODO: Define WiFi init. / connection parameters.
            Does the device act as a client, access point or an ad-hoc node?
            What parameters can you support?
            What parameters WILL you support?
            The Wi-Fi standard (802.11x) is HUGE.
            Before making this API, learn more about how Wi-Fi works. -->

            - 0x22    BLE_START   Initialize BLE
<!--        TODO: Define BLE init. / connection parameters.
            Before making this API, learn more about how BLE works. -->

            - 0x23    ADC_READ    Read analog pin
<!--        TODO: Is this specific only to MAXIM??? -->

            - 0x24    PWM_START   Start PWM
<!--        TODO: Describe this more... very open ended... -->

            - 0x25    UART_SEND   Send UART data
<!--        TODO: What is this used for? (IMPORTANT TO ANSWER BEFORE NEXT QUES.)
            Where is the corresponding UART_RECV?
            Define parameters such as baud rate. -->

        d. Create instructions for querying runtime information.
            - 0x30    GET_CPU_USAGE
            - 0x31    GET_FREE_RAM
            - 0x32    GET_TEMPERATURE
            - 0x33    GET_ACTIVE_TASKS

            - runtime statistics should help scheduling decisions on the host
            - runtime monitoring should minimize impact on device performance