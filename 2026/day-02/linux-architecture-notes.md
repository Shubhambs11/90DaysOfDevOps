# How Linux Works Under the Hood 🐧

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
