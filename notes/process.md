# Key Concepts in Operating Systems

## Virtualization
- **Definition**: Abstraction that allows multiple simulated environments on a single physical machine.
- **Purpose**: Enables efficient resource sharing by simulating multiple virtual instances (e.g., virtual CPUs, memory).
- **Mechanisms**:
  - **Time-sharing**: Runs multiple processes by dividing CPU time.
  - **Memory Virtualization**: Provides isolated address spaces for each process.

## Concurrency
- **Definition**: Simultaneous execution of processes or threads within a system.
- **Purpose**: Improves system efficiency by allowing multiple tasks to progress independently.
- **Challenges**:
  - **Synchronization**: Ensuring correct order and data integrity.
  - **Deadlock**: Avoiding situations where processes wait indefinitely for each other.
- **Mechanisms**:
  - **Locks and Semaphores**: Controls access to shared resources.
  - **Thread Scheduling**: OS schedules threads/processes to optimize resource usage.

## Persistence
- **Definition**: Long-term storage of data beyond process or system lifetimes.
- **Purpose**: Ensures data remains available after a system shutdown or crash.
- **Mechanisms**:
  - **File Systems**: Structured storage of files on disk.
  - **Databases**: Organized storage with complex querying and data manipulation capabilities.
  - **Storage Redundancy**: Techniques like RAID to prevent data loss.

# Notes on OS CPU Virtualization

## Illusion of Multiple CPUs
- **Technique**: The OS virtualizes CPUs by **time-sharing**—running one process, then pausing it to run another.
- **Outcome**: Creates the illusion of multiple CPUs, allowing multiple processes to run concurrently.
- **Trade-Off**: Performance may decrease as processes share CPU time.

## Mechanisms & Policies
- **Mechanisms**: Low-level methods (e.g., **context switching**) enable the OS to switch between processes on a CPU.
- **Policies**: High-level algorithms for decision-making (e.g., **scheduling policy**) decide which process to run next.
  - **Factors in Policies**:
    - Historical usage
    - Workload types
    - Performance metrics (e.g., interactive performance vs. throughput)

# The Abstraction: A Process

## Definition
- A **process** is the OS-provided abstraction of a running program.
- It represents the **machine state** that the program accesses or modifies during execution.

## Components of a Process
1. **Memory (Address Space)**:
   - Contains instructions and data accessed by the process.
   - Defines the addressable memory range for the process.

2. **Registers**:
   - Key part of the machine state, as instructions often read from or write to registers.
   - Includes special registers:
     - **Program Counter (PC)**: Points to the next instruction.
     - **Stack Pointer** and **Frame Pointer**: Manage the stack for function parameters, local variables, and return addresses.

3. **Persistent Storage (I/O State)**:
   - Includes any I/O devices or files the process is currently accessing or has open.

# Process API

## Essential Functions of a Process API
These APIs are core components available in all modern operating systems for process management.

1. **Create**:
   - Allows the OS to create new processes.
   - Triggered by actions like typing a command or launching an application.

2. **Destroy**:
   - Provides an interface to terminate processes.
   - Useful for forcefully ending runaway processes that do not exit on their own.

3. **Wait**:
   - Allows the system or user to wait until a process finishes executing.

4. **Miscellaneous Control**:
   - Additional controls for process management, including:
     - **Suspend**: Temporarily halts a process.
     - **Resume**: Restarts a suspended process.

5. **Status**:
   - Provides information on process attributes, such as:
     - Runtime duration
     - Current state

# Process Creation: Detailed Steps

## 1. Loading Code and Static Data
- **Objective**: Load program code and static data (e.g., initialized variables) into the process's memory space.
- **Process**:
  - Programs start as executable files on disk (or SSD).
  - **Eager Loading**: In simple systems, the OS loads the entire program into memory at once.
  - **Lazy Loading**: In modern systems, the OS loads pieces of code/data as needed during execution.

## 2. Stack Initialization
- **Purpose**: Allocate memory for the program’s **run-time stack**.
  - The stack holds **local variables**, **function parameters**, and **return addresses**.
- **Argument Setup**: OS initializes the stack with `argc` and `argv` for the main function.

## 3. Heap Allocation
- **Purpose**: Allocate space for the program’s **heap**, used for dynamic memory (e.g., `malloc` in C).
  - The heap size starts small and grows as needed by the program, with additional memory allocated by the OS when required.

## 4. I/O Initialization
- **Standard I/O**:
  - In UNIX systems, each process has three default file descriptors:
    - **Standard Input** (stdin)
    - **Standard Output** (stdout)
    - **Standard Error** (stderr)
  - These descriptors enable the process to handle terminal input and output easily.

## 5. Start Program Execution
- **Final Step**: The OS transfers control to the process at the entry point, `main()`.
  - This transition is done through a specialized mechanism, transferring CPU control to the new process, allowing it to begin execution.

# Process States

## 1. New
- **Definition**: Process is being created.
- **Actions**: OS allocates resources and prepares the process for execution.

## 2. Ready
- **Definition**: Process is ready to run but is waiting for CPU allocation.
- **State Transition**: Moves to "Running" when the CPU becomes available.

## 3. Running
- **Definition**: Process is currently being executed on the CPU.
- **Actions**: Executes instructions and performs operations.
- **State Transition**:
  - Moves to "Waiting" if it needs I/O or other resources.
  - Moves to "Terminated" if it completes execution.
  - Moves to "Ready" if preempted by the OS (e.g., time slice ends).

## 4. Waiting (Blocked)
- **Definition**: Process is waiting for an external event (e.g., I/O completion).
- **State Transition**: Moves back to "Ready" when the event occurs.

## 5. Terminated
- **Definition**: Process has completed execution.
- **Actions**: OS deallocates resources and removes the process from the process table.

# OS Data Structures

## Key Points:
- **Purpose**: Like any program, the OS uses data structures to track important information, such as process states and resources.

## Process Tracking
- **Process List**: 
  - The OS maintains a list of all processes, particularly those that are **ready to run**.
  - Additional information tracks the **currently running process**.
- **Blocked Processes**:
  - The OS tracks processes that are **blocked**, waiting for I/O or other events.
  - When an event completes, the OS **wakes** the correct process and moves it back to the ready state.

## Process Information
- The **process structure** (as shown in xv6 and other OSes like Linux, MacOS, and Windows) tracks critical information:
  - **Register context**: 
    - Holds the values of the process's registers when the process is stopped. These values are restored when the OS **resumes** the process.
    - Key for performing **context switches**, allowing the OS to save and resume processes.
    
## Process States
- **Standard States**:
  - **Running**: The process is actively executing.
  - **Ready**: The process is ready to execute, waiting for CPU time.
  - **Blocked**: The process is waiting for an event, like I/O completion.
  
- **Additional States**:
  - **Initial State (Embryo)**: When a process is being created.
  - **Zombie State**: A process that has exited but has not been fully cleaned up yet. 
    - Useful in UNIX-based systems for **examining return codes** (e.g., to determine success or failure).
    - The parent process calls `wait()` to clean up the process and release OS resources.

## Summary
- The OS uses data structures to track process states, blocked processes, and other resources.
- **Context switching** is a critical technique that saves a stopped process's state and restores it later.
- Processes can also exist in special states like **zombie**, where they’ve completed execution but await cleanup.
