## How Linux Works Under the Hood 🐧

This document explains **how Linux works internally**, from booting to process execution, in a simple and structured way.

---

## 1. Linux Boot Process

1. **BIOS / UEFI**
   - Initializes hardware
   - Loads the bootloader

2. **Bootloader (GRUB)**
   - Selects the OS
   - Loads the Linux kernel into memory

3. **Kernel Initialization**
   - Detects CPU, RAM, disks, devices
   - Mounts root filesystem
   - Starts the first process (`systemd` or `init`, PID = 1)

4. **Init System (systemd)**
   - Starts services (network, GUI, SSH, etc.)
   - Brings system to usable state

---

## 2. Linux Kernel (Core of the OS)

The **kernel** runs in *kernel space* and has full access to hardware.

### Kernel Responsibilities
- **Process Management**
- **Memory Management**
- **Device Drivers**
- **File System Management**
- **Networking**
- **System Calls**

---

## Core Components of Linux 🐧

Linux is built around a few fundamental components that work together to provide a complete operating system.

---

## 1. Linux Kernel

The **kernel** is the core of Linux. It runs in **kernel space** and has full control over the system hardware.

### Responsibilities of the Kernel
- **Process Management**
  - Creates and terminates processes
  - CPU scheduling
  - Context switching

- **Memory Management**
  - Virtual memory
  - Paging and swapping

- **Device Management**
  - Hardware abstraction via device drivers
  - Handles I/O operations

- **File System Management**
  - Virtual File System (VFS)
  - Supports ext4, xfs, btrfs, etc.

- **Networking**
  - TCP/IP stack
  - Socket interface

- **System Calls**
  - Interface between user space and kernel

### Kernel Space
- Executes privileged instructions
- Direct access to hardware

---

## 2. User Space

**User space** contains all applications and utilities that run outside the kernel.

### Components of User Space
- **Shell**
  - `bash`, `zsh`, `fish`
- **System Utilities**
  - `ls`, `cp`, `mv`, `grep`
- **Applications**
  - Browsers, editors, servers
- **Libraries**
  - `glibc`, `libstdc++`

### Characteristics
- No direct hardware access
- Interacts with kernel via **system calls**
- Fault isolation (crash doesn’t affect kernel)

---

## 3. Init System (systemd / init)

The **init system** is the first user-space process started by the kernel.

### Key Facts
- Process ID: **PID = 1**
- Responsible for system startup and shutdown

### Responsibilities
- Starts and stops services
- Manages system targets (runlevels)
- Handles logging, networking, time sync
- Restarts failed services

### Common Init Systems
- **systemd** (most modern distros)
- SysV init (legacy)
- Upstart (deprecated)

### systemd Components
- `systemctl` – service manager
- `journald` – logging
- `udevd` – device management
- `networkd` – networking

---

## 4. Interaction Between Components

```text
User Application
       ↓
System Call Interface
       ↓
Linux Kernel
       ↓
Hardware

```

### - BIOS/UEFI → Bootloader → Kernel → systemd (PID 1) → User Services

```test
Kernel   → Manages hardware and resources
User Space → Runs applications and utilities
Init System → Brings system to operational state
```

## How Processes Are Created and Managed in Linux 🐧

A **process** is a running instance of a program. Linux provides a powerful and efficient process management system handled by the **kernel**.

---

## Linux Process Creation and Management – In-Depth Explanation 🐧

This document provides a **deep, kernel-level explanation** of how Linux creates, schedules, manages, and terminates processes.  
It is suitable for **OS internals study, interviews, and system-level understanding**.

---

## 1. What Is a Process in Linux?

A **process** is an executing program along with all the resources required to run it.

Each process consists of:
- Executable code (text segment)
- Data segment (global/static variables)
- Heap (dynamic memory)
- Stack (function calls, local variables)
- CPU context (registers, program counter)
- Kernel metadata

In the kernel, a process is represented by a `task_struct`.

---

## 2. Process Representation: `task_struct`

Every process has a **Process Control Block (PCB)** implemented as `task_struct`.

Key fields include:
- `pid` – Process ID
- `state` – Current process state
- `mm` – Memory descriptor
- `files` – Open file descriptors
- `parent` / `children`
- `sched_entity` – Scheduler information
- `cred` – Security credentials

All `task_struct` objects are stored in the kernel’s process list.

---

## 3. Process Creation: fork–exec Model

Linux follows a **two-step process creation model**.

---

## 4. `fork()` – Creating a Process

`fork()` creates a new process by duplicating the calling process.

### What Happens Internally
- Kernel allocates a new `task_struct`
- Child gets a new PID
- Parent and child share memory pages
- Uses **Copy-On-Write (COW)**

### Copy-On-Write (COW)
- Parent and child share the same physical pages
- Pages are marked read-only
- Actual copy occurs only when a write happens

This makes `fork()` extremely fast.

---

## 5. `exec()` – Replacing Process Image

`exec()` replaces the current process memory with a new program.

### What `exec()` Does
- Destroys old address space
- Loads new executable into memory
- Initializes stack, heap, and registers
- Keeps the same PID

After `exec()`, the process is logically a **new program**.

---

## 6. Process States

Linux defines several process states:

| State | Description |
|----|------------|
| `TASK_RUNNING` | Running or ready to run |
| `TASK_INTERRUPTIBLE` | Sleeping, can be interrupted |
| `TASK_UNINTERRUPTIBLE` | Waiting on I/O |
| `TASK_STOPPED` | Stopped by signal |
| `TASK_ZOMBIE` | Terminated, not reaped |

States are managed by the kernel scheduler.

---

## 7. Process Scheduling

Linux uses the **Completely Fair Scheduler (CFS)**.

---

## 8. Completely Fair Scheduler (CFS)

CFS models **ideal multitasking** by sharing CPU equally.

### Core Concepts
- **Virtual Runtime (vruntime)**
- Lower vruntime → higher priority
- Red-Black Tree stores runnable processes

### Scheduling Flow
1. Pick process with lowest vruntime
2. Run it for a time slice
3. Update vruntime
4. Reinsert into tree

This ensures fairness and prevents starvation.

---

## 9. Context Switching

A **context switch** occurs when the CPU switches processes.

### Steps
- Save current CPU registers
- Save program counter
- Update process state
- Load next process context

Context switches are costly, so Linux minimizes them.

---

## 10. Parent and Child Relationship

Every process (except PID 1) has a parent.

- Parent tracks child status
- Uses `wait()` or `waitpid()`

If a parent exits:
- Child becomes an **orphan**
- Adopted by `systemd` (PID 1)

---

## 11. Zombie Processes

A **zombie** process:
- Has finished execution
- Still has an entry in process table
- Exists until parent calls `wait()`

Purpose:
- Allows parent to read exit status

Too many zombies indicate a buggy parent process.

---

## 12. Process Termination

A process can terminate by:
- Calling `exit()`
- Returning from `main()`
- Receiving a fatal signal

### Kernel Cleanup
- Releases memory
- Closes files
- Sends exit signal to parent
- Moves process to zombie state

---

## 13. Signals and Process Control

Signals are asynchronous notifications.

### Common Signals
- `SIGTERM` – Graceful termination
- `SIGKILL` – Force termination
- `SIGSTOP` – Pause execution
- `SIGCONT` – Resume execution

Signals are handled by kernel or user-defined handlers.

---

## 14. Process Priorities and Nice Values

Linux supports dynamic priority.

- `nice` range: `-20` (highest) to `+19` (lowest)
- Affects scheduler decision
- Lower nice → more CPU time

---

## 15. Process Isolation and Security

Linux isolates processes using:
- Separate virtual memory
- User IDs (UID/GID)
- Capabilities
- Namespaces (containers)

This prevents processes from interfering with each other.

---

## 16. Summary

```text
Process = Program + Resources + Kernel Metadata
fork()  → duplicate process
exec()  → load new program
CFS     → fair CPU scheduling
Signals → control execution
exit()  → terminate process

