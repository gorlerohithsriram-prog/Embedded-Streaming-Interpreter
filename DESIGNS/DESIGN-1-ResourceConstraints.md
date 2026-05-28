<!--
Copyright (C) 2026 Chinmay: Coding Club, IIT Dharwad - All Rights Reserved
You may use, distribute and modify this code under the
terms of the MIT license.

You should have received a copy of the MIT license with
this file. If not, please write to: codingclub@iitdh.ac.in, or visit 
https://mit-license.org/
-->

1.  Resource Constraints

    1. Determining suitable tasks for each kind of development board

        a. Define the capabilities of each board (e.g. WiFi/BLE, CNN accel.).
            - MAXIM78000FTHR:
                - 512KB Flash Memory
                - 128 KB SRAM
                - 16KB Cache
                - CNN Data RAM -> 512 KB
                - 17 Direct GPIO pins
                - CNN Accelerator: Ultra-low power usage without waking the main
                CPU
                - Compatible with Adafruit FeatherWings to add missing external
                connectivity (WiFi, BLE, LoRa)

            - ESP32 XIAO:
<!--            TODO: Get details from others who have the XIAO board  -->
                - ???

        b. Explain in detail how each capability can be configured and used.
            - MAXIM78000FTHR:
<!--            TODO: Learn more about how to configure the accelerator. 
                (https://github.com/analogdevicesinc/ai8x-synthesis)
                Learn more about Pytorch, basics of CNNs and how they work. -->
                - Configuration of the CNN accelerator: ???

            - ESP32 XIAO:
<!--            TODO: Get details from others who have the XIAO board  -->
                - ???

        c. Mention any limitations or drawbacks of using each of said
        capabilities (e.g. reflashing required, lost parallelism).
            - MAXIM78000FTHR:
                - Reflashing Required for CNN accelerator:
                The CNN model weights are compiled directly into the firmware.
                Updating the AI model requires entirely recompiling and
                reflashing the microcontroller.

            - ESP32 XIAO:
<!--            TODO: Get details from others who have the XIAO board  -->
                - ???