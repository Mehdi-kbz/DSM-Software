# Distributed Shared Memory (DSM) System

<img width="621" alt="Screenshot 2025-01-19 at 6 52 55 PM" src="https://github.com/user-attachments/assets/07149c2a-1ff9-4dc6-b2b7-cdde1dab0162" />
<img width="621" alt="Screenshot 2025-01-19 at 6 53 19 PM" src="https://github.com/user-attachments/assets/f083c4fd-c289-41c0-99e6-5c25276a1f76" />


---

## Table of Contents
- [Introduction](#introduction)
- [Project Objectives](#project-objectives)
- [Features](#features)
- [Architecture](#architecture)
- [Components](#components)
- [How It Works](#how-it-works)
  - [Launching Processes](#launching-processes)
  - [Memory Management](#memory-management)
  - [Synchronization Protocols](#synchronization-protocols)
- [Setup and Installation](#setup-and-installation)
- [Usage](#usage)
- [Challenges Faced](#challenges-faced)
- [Future Improvements](#future-improvements)
- [Contributors](#contributors)

---

## Introduction

The **Distributed Shared Memory (DSM) System** is a project that implements a shared memory model over a distributed network of processes. It creates the illusion of a unified memory space for processes running on different machines, enabling seamless memory access and synchronization.

DSM systems have applications in parallel and distributed computing, providing a simplified programming model for applications requiring data sharing across multiple nodes.

---

## Project Objectives

1. **Unified Memory Access**: Abstract the complexity of memory sharing in distributed systems.
2. **Inter-Process Communication (IPC)**: Implement efficient IPC mechanisms using sockets.
3. **Scalability**: Design a system capable of handling multiple processes distributed across different nodes.
4. **Error Handling**: Manage segmentation faults and ensure fault-tolerant operations.

---

## Features

- **Dynamic Memory Sharing**: Allocates and transfers memory pages on-demand among processes.
- **Process Management**: Launches and monitors processes on multiple nodes.
- **Page Ownership**: Tracks ownership of memory pages and synchronizes state changes.
- **Error Handling**: Resolves segmentation faults using signal handlers.
- **Centralized Output Logging**: Redirects and collects logs from all processes for centralized monitoring.

---

## Architecture

The architecture is divided into three main components:

1. **Process Management**:
   - Launches processes on distributed nodes.
   - Manages ranks and connections between processes.

2. **Memory Management**:
   - Handles page allocation and transfer using TCP sockets.
   - Resolves segmentation faults through page request and synchronization protocols.

3. **Synchronization**:
   - Maintains consistency across distributed memory using mutexes and locking mechanisms.

---

## Components

### 1. **dsmexec**
The main launcher responsible for:
- Reading the machinefile to identify nodes.
- Launching processes on specified nodes using `ssh`.
- Establishing initial connections and rank assignments.

### 2. **dsmwrap**
An intermediary that:
- Sets up communication between the launcher and the child processes.
- Executes the target application with the required arguments.

### 3. **Memory Management Layer**
- Implements the `SIGSEGV` signal handler to detect invalid memory access.
- Manages memory page requests and ownership transfer.

---

## How It Works

### Launching Processes
1. **Initialization**:
   - `dsmexec` reads the machinefile and launches processes on each node via `ssh`.
   - Each process communicates with the launcher to establish its rank and connection.

2. **Rank Assignment**:
   - Each process is assigned a unique rank, which determines its role in the memory allocation scheme.

3. **Output Redirection**:
   - The `stdout` and `stderr` of all processes are redirected to the launcher for centralized logging.

---

### Memory Management
1. **Segmentation Fault Handling**:
   - A custom `SIGSEGV` handler detects memory violations.
   - The handler identifies the missing memory page and requests it from the owner.

2. **Page Ownership**:
   - Pages are initially distributed among processes in a round-robin manner.
   - Ownership changes dynamically based on access patterns.

3. **Page Transfer**:
   - Memory pages are transferred over TCP sockets using a structured protocol.
   - The protocol ensures data integrity and synchronization.

---

### Synchronization Protocols
1. **Consistency**:
   - Ensures all processes have a consistent view of shared memory.
   - Mutexes are used to prevent race conditions during page updates.

2. **Fault Tolerance**:
   - Handles unexpected disconnections and retries page requests.

---

## Setup and Installation

### Prerequisites
- Unix/Linux environment.
- GCC or any compatible C compiler.
- `ssh` and network access between nodes.
- Shared file system for accessing the executable and configuration files.

### Installation
1. **Download the Repository**:
   ```bash
   git clone https://github.com/yourusername/dsm-project.git
   cd dsm-project
   ```
2. Build the Project:
   ```bash
   make
   ```
   
3. Set Environment Variables: Add the following to your .bashrc or .zshrc:
   ```bash
   export DSM_BIN=/path/to/dsm-project/bin
   export PATH=$DSM_BIN:$PATH
   ```
---

## Usage

1. **Create a Machinefile**:
   - List all the machines (nodes) on which you wish to run the distributed processes. Each machine should be listed on a separate line.
   - Example `machinefile`:
     ```
     node1
     node2
     node3
     ```

2. **Run the DSM Application**:
   - Use the `dsmexec` launcher to start the distributed application.
   - Syntax:
     ```bash
     ./bin/dsmexec machinefile executable [args...]
     ```
   - Example:
     ```bash
     ./bin/dsmexec machinefile ./sample_program arg1 arg2
     ```

3. **Monitor Output**:
   - The `stdout` and `stderr` of all processes will be collected by `dsmexec` and displayed in the terminal.
   - Logs are stored for debugging and analysis.

4. **Debugging**:
   - If there are issues, check the connection between nodes and ensure all nodes can access the shared files and executables.

---

## Challenges Faced

### 1. **Debugging Inter-Process Communication (IPC)**:
   - Establishing and maintaining reliable TCP connections between processes was a major challenge.
   - Ensuring proper synchronization across processes without introducing deadlocks required extensive testing.

### 2. **Handling Segmentation Faults**:
   - Implementing a robust `SIGSEGV` handler that works seamlessly across processes and nodes was complex.
   - We encountered issues with edge cases, such as simultaneous access to the same memory page.

### 3. **Scalability**:
   - Designing a scalable system that supports a large number of processes and memory pages posed architectural challenges.
   - Optimizing page transfer protocols for minimal latency and high throughput was crucial.

---

## Future Improvements

1. **Dynamic Process Management**:
   - Add support for dynamically joining and removing processes during execution.
   - Introduce load balancing to distribute memory pages more efficiently.

2. **Enhanced Monitoring and Debugging Tools**:
   - Develop tools to visualize memory allocation and page transfer in real time.
   - Include detailed logging and performance metrics.

3. **Optimized Communication**:
   - Implement advanced communication protocols, such as RDMA or gRPC, to improve data transfer speeds.

4. **Support for Advanced Consistency Models**:
   - Extend the DSM system to support consistency models such as causal consistency or eventual consistency, in addition to strict consistency.

---

## Contributors

- **Mehdi Khabouze**: Lead Developer, Architect of the DSM System
- **Yassine Hamed**: Assisted with Testing, Documentation, and Debugging

---

## License

This project is licensed under the [MIT License](https://opensource.org/licenses/MIT). You are free to use, modify, and distribute this project as per the license terms.

