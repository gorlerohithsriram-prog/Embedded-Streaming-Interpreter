# Requirements

1.  Resource Constraints
    1. Determining suitable tasks for each kind of development board
        a. Define the capabilities of each board (e.g. WiFi/BLE, CNN accel.).
        b. Explain in detail how each capability can be configured and used.
        c. Mention any limitations or drawbacks of using each of said
        capabilities (e.g. reflashing required, lost parallelism).
    2. Quantifying performance and device stress to be used in scheduling of
    kernels
        a. Determine flash size and RAM size of each board.
        b. Stress test IO over USB, measure average throughput over runs.
        c. Stress test GPIO pins between two boards of the same model,
        measure average throughput over runs.
        d. Stress test GPIO pins between pairs of boards of different models,
        measure average throughput over runs.
        e. Stress test other capabilities after eliminating IO side effects.

2.  Communication
    1. Creating a protocol that will be used to send and receive packets of
    instructions, data and metadata.
        a. Design a data link layer agnostic protocol. Choose a suitable
        statefulness for the protocol (connection based or not). Explain at a
        high level how the handshaking mechanism will work if it is connection
        based.
        b. Design a extensible but efficient packet structure that can
        encapsulate details related to instruction batches, data batches and any
        other protocol specific interactions.
        c. Mention bit width and valid values for each field.
        d. Program sender and receiver functions for the host and various
        devices that can parse the packets efficiently.
    2. Creating an Intermediate Representation (IR) that can capture semantics
    of instructions and data development boards
        a. Create instructions for querying device information.
        b. Create instructions for input/output between boards.
        c. Create instructions for each capability supported in the boards.
        d. Create instructions for querying runtime information.

3. Scheduling
    1. Designing and developing scheduling algorithms that will determine the
    placement of kernels across devices.
        a. Determine what are the hard and soft parameters that will be used in
        scheduling (e.g. Capability limitations, maximum number of concurrent
        threads supported by a device are hard parameters, waiting time/numbers
        related to CPU/memory usage are soft parameters).
    2. Designing mini-schedulers that can run on the devices in case multiple
    kernels are allowed to be issued at a time.
        a. Create watchdog timers based on approximate kernel load.
        b. Include concepts of task priorities and CPU and IO time.
        c. Calculate expected CPU time to be given based on whether task is I/O
        heavy or CPU heavy.

4. Infrastructure
    1. Creating suitable data structures that can keep track of information
    related to the host, devices and the links.
        a. Keep track of jobs to device mapping.
        b. Large jobs should have ways to be split into concurrent sub-jobs
        scheduled on different devices.
        c. Keep track of the link information between devices in a graph
        structure.
    2. Identifying algorithms that that can be used to keep track of the
    topology of the connected devices.
        a. Create suitable heuristics to help caculate optimal paths from device
        to device.
    3. Creating firmware that runs on the development boards and interface with
    the host device at a logical level.
    4. Developing host side software that can provide a common interface/API to
    connect with various types of boards.
    5. Allowing debugging information of the host and devices to be accessed
    via a suitable logging format.
        a. Specific info should be exposed to the user that can be logged using
        commands or code.
        b. Host time and device time should be separate from each other.
        b. Frequency of logging debug information should be configurable.