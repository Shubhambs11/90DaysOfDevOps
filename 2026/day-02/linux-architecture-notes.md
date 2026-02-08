## Boot Process (How Linux Starts)

BIOS/UEFI

Powers on hardware

Loads the bootloader

Bootloader (GRUB)

Lets you choose OS

Loads Linux kernel into memory

Kernel Initialization

Detects CPU, RAM, disks, drivers

Mounts root filesystem

Starts the first process: init / systemd (PID = 1)

systemd (init system)

Starts services (network, GUI, SSH, etc.)

Brings system to usable state

## Kernel (The Core of Linux)

The kernel is the boss. It runs in kernel mode (full hardware access).

Kernel Responsibilities

Process Management

Creates processes (fork)

Schedules CPU time (scheduler)

Memory Management

Virtual memory

Paging & swapping

Device Drivers

Keyboard, disk, network, USB

File System

ext4, xfs, btrfs

System Calls

Interface between user programs & kernel

👉 User programs never access hardware directly.
