<!--
Copyright (C) 2026 Devansh: Coding Club, IIT Dharwad - All Rights Reserved
You may use, distribute and modify this code under the
terms of the MIT license.

You should have received a copy of the MIT license with
this file. If not, please write to: codingclub@iitdh.ac.in, or visit 
https://mit-license.org/
-->

4. Infrastructure

    1. Creating suitable data structures that can keep track of information
    related to the host, devices and the links.

        a. Keep track of jobs to device mapping.
            Tasks:
                - Task ID
                - Parent ID
                - Time of Dispatch
                - Start Time
                - Completion time (if completed)
                - Pointer to device struct
<!--            TODO: Specify what will be in resource req. field -->
                - Resources required
                - Current Task State (SUBMITTED, PENDING, RUNNING, COMPLETED,
                  ERROR, BLOCKED)

            Device:
                - Device ID
                - Device type (ESP or MAX board)
                - Neighbours
                - Queue information (Tasks pending, Max size of the queue)
                  & id's of the task in the queue
<!--            TODO: Specify what will be in the free resources field -->
                - Free resources in the device
                - Status of the device whether it is busy or not
                - Pointer to task struct

        b. Large jobs should have ways to be split into concurrent sub-jobs
        scheduled on different devices.
            - Tasks are split forked into sub-tasks if
                - task resource req. are larger than available on device, and
                - if parallelization provides benefits.

            - Scheduler must have to parse the task and auto-parallelization of
              into possible subtasks
<!--          TODO: Something similar to the LLVM opt? Long term plan. -->
                - ???

            - User must be able to provide hints in the IR where
              parallelization can be exploited
<!--          TODO: Same comment as above -->
                - ???

        c. Keep track of the link information between devices in a graph
        structure.
            - We will store all the information of the devices, host and links
              by making appropriate structures and update them regularly.

            - The source node in the graph will represent the host and other
              nodes will be representing devices.

            - Each edge will represent the link between nodes, containing
              information like latency, error rate and other to measure the
              weight of the edge along with the information stored in the node.

    2. Identifying algorithms that that can be used to keep track of the
    topology of the connected devices.
        a. Create suitable heuristics to help caculate optimal paths from device
        to device.
            - Minimum weighted paths will be used to send data.

            - If the device has no slot for the task (or it does not have the
              required capabilities), an infinite weight will be used for all of
              its neighbouring edges (essentially discarding it)

            - Each path's weight will depend on the multiple factors:
                - Total Delay to reach from Host to Scheduled device (Latency).
                - Expected time when device will begin to do the selected task
<!--        TODO: Define the coefficients of the linear combination -->
            - Path weight is linear combination of the above factors
<!--        TODO: Is TTL used for breaking routing loops? -->
            - Ensure that it doesn't stuck in any kind of loop using TTL.

    3. Creating firmware that runs on the development boards and interface with
    the host device at a logical level.
        <!-- TODO: Create the firmware -->
        - ???

    4. Developing host side software that can provide a common interface/API to
    connect with various types of boards.
        <!-- TODO: Define the user facing API -->
        - ???

    5. Allowing debugging information of the host and devices to be accessed
    via a suitable logging format.
        a. Specific info should be exposed to the user that can be logged using
        commands or code.
            <!-- TODO: Fulfil the requirement. -->
            - ???
        b. Host time and device time should be separate from each other.
            <!-- TODO: Define how to extract device time and how to sync. -->
            - ???
        c. Frequency of logging debug information should be configurable.
            <!-- TODO: First of all, what is logged should be defined.-->
            - ???
