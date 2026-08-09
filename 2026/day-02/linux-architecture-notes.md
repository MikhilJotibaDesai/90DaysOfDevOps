# Day 02 – Linux Architecture, Processes, and systemd

## 1. Linux Architecture

Linux is an operating system kernel that manages the communication between hardware and software. A Linux system can broadly be understood as several layers:

```text
User Applications
       ↓
User Space
       ↓
System Calls
       ↓
Linux Kernel
       ↓
Hardware
```

### 1.1 Linux Kernel

The kernel is the core component of a Linux operating system. It runs in a privileged mode and manages the system's hardware and resources.

The kernel is responsible for:

* **Process Management** – Creating, scheduling, and terminating processes.
* **Memory Management** – Allocating and managing RAM for processes.
* **Device Management** – Communicating with hardware devices through device drivers.
* **File System Management** – Providing access to files and storage devices.
* **Networking** – Managing network communication and network interfaces.
* **Security and Permissions** – Enforcing access controls and privileges.
* **System Calls** – Providing an interface through which user-space programs can request kernel services.

### 1.2 User Space

User space is the area where applications and utilities run without direct access to hardware or kernel memory.

Examples include:

* Bash and other shells
* Text editors
* Web servers such as Nginx and Apache
* Databases
* SSH
* Linux commands such as `ls`, `cp`, and `ps`

Applications in user space interact with the kernel through **system calls**.

For example, when a program needs to read a file, it requests the kernel to perform the operation instead of directly accessing the storage device.

### 1.3 System Calls

System calls provide a controlled interface between user-space applications and the Linux kernel.

Common system-call operations include:

* Creating processes
* Reading and writing files
* Allocating memory
* Creating network connections
* Communicating with devices

This separation helps protect the kernel and other processes from incorrect or unauthorized operations.

---

## 2. Linux Processes

A **process** is a running instance of a program.

For example, when you start a web server, the program running in memory becomes a process.

Each process has its own:

* Process ID (PID)
* Memory space
* State
* Open files
* Security credentials
* Parent process

### 2.1 Process ID (PID)

Linux assigns a unique numerical identifier called a **PID** to every running process.

The PID allows the operating system and administrators to identify and manage individual processes.

The first user-space process started by the kernel normally has **PID 1**.

### 2.2 Parent and Child Processes

Processes in Linux form a hierarchical relationship.

A process can create another process, making the original process the **parent** and the newly created process the **child**.

The hierarchy can be represented as:

```text
Parent Process
      │
      ├── Child Process
      │      └── Grandchild Process
      │
      └── Child Process
```

This creates a process tree.

### 2.3 Creating Processes

Linux traditionally uses the `fork()` system call to create a new process.

The child process initially receives a copy of the parent's process environment. The child can then use `exec()` to replace its process image with another program.

A simplified process creation flow is:

```text
Parent Process
      ↓
    fork()
      ↓
Child Process
      ↓
    exec()
      ↓
New Program
```

### 2.4 Process States

A process can exist in different states during its lifetime.

Common states include:

* **Running** – Currently executing or ready to execute.
* **Sleeping** – Waiting for an event or resource.
* **Stopped** – Execution has been suspended.
* **Zombie** – The process has finished execution, but its parent has not yet collected its exit status.

The kernel's scheduler determines which processes get CPU time.

---

## 3. init and systemd

After the Linux kernel initializes the hardware and core subsystems, it starts the first user-space process.

This process traditionally came from the **init** system.

Modern Linux distributions commonly use **systemd** as their init and service-management system.

The basic startup sequence can be represented as:

```text
Bootloader
    ↓
Linux Kernel
    ↓
systemd (PID 1)
    ↓
System Services
    ↓
User Applications
```

### 3.1 What is systemd?

**systemd** is a system and service manager used by many modern Linux distributions.

It is responsible for managing the system from early boot until shutdown.

Its responsibilities include:

* Starting services during system boot
* Stopping services during shutdown
* Restarting failed services
* Managing service dependencies
* Managing system targets
* Tracking running services and processes
* Managing system logs through `journald`
* Managing various system resources

### 3.2 systemd as PID 1

On a system using systemd, it normally runs as **PID 1**.

Because PID 1 is responsible for managing the system's initial user-space processes, systemd plays a critical role in the overall operation of the system.

If a process becomes orphaned because its original parent terminates, PID 1 can adopt that process.

### 3.3 systemd Units

systemd manages different types of resources using **units**.

Common unit types include:

| Unit Type  | Purpose                       |
| ---------- | ----------------------------- |
| `.service` | Manages system services       |
| `.socket`  | Manages communication sockets |
| `.target`  | Groups units together         |
| `.timer`   | Schedules tasks               |
| `.mount`   | Manages mount points          |
| `.path`    | Monitors filesystem paths     |

A service such as SSH can therefore be represented by a systemd service unit.

### 3.4 systemctl

`systemctl` is the primary command-line tool used to interact with systemd.

It can be used to:

* Start services
* Stop services
* Restart services
* Enable services at boot
* Disable services
* Check service status
* List active units

For example:

```text
systemctl
    ↓
systemd
    ↓
Service Management
```
---
## Commanly used linux commands:

1. ls          → files
2. cd          → navigation
3. pwd         → where am I?
4. cat         → read files
5. grep        → search
6. find        → locate files
7. ps          → processes
8. systemctl   → services
9. df          → disk usage
10. tail       → logs

---
## Diagrams:
  

                         LINUX SYSTEM
                              │
                              ▼
                    ┌─────────────────┐
                    │     Hardware    │
                    │ CPU • RAM • Disk│
                    │ Network • I/O   │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │  Linux Kernel   │
                    │                 │
                    │ • Processes     │
                    │ • Memory        │
                    │ • Filesystems    │
                    │ • Networking     │
                    │ • Drivers       │
                    └────────┬────────┘
                             │
                       System Calls
                             │
                             ▼
                    ┌─────────────────┐
                    │    User Space   │
                    │                 │
                    │ • Bash          │
                    │ • SSH           │
                    │ • Nginx         │
                    │ • Applications  │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ systemd (PID 1) │
                    │                 │
                    │ Service Manager │
                    │ Boot Management │
                    │ Process Control │
                    │ Logging         │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
            nginx            ssh            cron
           service         service        service

## 4. Key Concepts

The relationship between the major concepts can be summarized as:

```text
Hardware
    ↓
Linux Kernel
    ↓
System Calls
    ↓
User-Space Programs
    ↓
Processes
    ↓
systemd manages system services
```

The most important points to remember are:

1. The **Linux kernel** manages hardware and system resources.
2. **User space** contains applications and utilities.
3. **System calls** provide an interface between user space and the kernel.
4. A **process** is a running instance of a program.
5. Every process has a **PID**.
6. Processes form a parent-child hierarchy.
7. `fork()` can create a new process, while `exec()` can replace the process with another program.
8. Modern Linux systems commonly use **systemd** as the init and service manager.
9. systemd normally runs as **PID 1**.
10. **systemctl** is used to manage systemd units and services.
11. systemd also manages service dependencies, startup, shutdown, and system logging.
