## the core components of Linux (kernel, user space, init/systemd).
.Kernel: The kernel is the core of the operating system and the bridge between software and hardware. It operates in a protected memory area called kernel space and is responsible for managing system resources such as:
Process management: Handles the creation, scheduling, and termination of processes, sharing the CPU among active tasks.
Memory management: Manages system memory (RAM), allocating and deallocating memory to processes efficiently.
Device drivers: Software components that allow the kernel to interact with hardware devices like network cards, storage, and graphics cards.
File systems: Provide a unified interface for accessing different file systems and managing data storage.

.User Space: This is the environment where all user applications, system libraries, and utilities run. Processes in user space cannot directly access hardware; they must interact with the kernel through the system call interface (SCI) to request resources or perform privileged operations. Key components within the user space include:
System.

.Init System (systemd): The init system is the first user-space process started by the kernel, assigned Process ID (PID) 1, and runs until the system shuts down.


## How processes are created and managed.
. Processes are created in operating systems when programs are launched, prompting the OS to allocate resources, assign a unique Process ID (PID), and create a Process Control Block (PCB) to track state, registers, and memory. Management involves scheduling (deciding which process runs next), IPC (inter-process communication), synchronization to prevent conflicts, and termination. 

## What systemd does and why it matters.

. systemd is a comprehensive system and service manager for Linux operating systems that serves as the default initialization system (init system) for almost all modern, major Linux distributions. As the first process to run during boot (PID 1), it brings up, configures, and maintains userspace services, replacing older, slower methods like SysVinit. 
