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

### BIOS/UEFI → Bootloader → Kernel → systemd (PID 1) → User Services

```test
Kernel   → Manages hardware and resources
User Space → Runs applications and utilities
Init System → Brings system to operational state
```



