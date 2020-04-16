# The Boot Process

[Boot Process]()

## Bios

Starting a x86-based Linux System Steps

- When Computer is powered on
- **B**asic **I**nput/**O**utput **S**ystem (BIOS) initializes the hardware.
  - Screen and keyboard
  - Tests the main memory
  - Called  **POST** - **P**ower **O**n **S**elf **T**est
- BIOS software is stored on a ROM(**R**ead-**O**nly **M**emory ) chip on the motherboard.  
- After this, the remainder of the boot process is controlled by the operating system (OS).

## Master Boot Record(MBR) and Boot Loader

- Once **POST** completes, system control passes from **BIOS** to the **boot loader**

### Boot Loader

- Stored on one of the hard disks in the system either in 
  -  Boot sector( traditional **BIOS/MBR** system)  or
  - **EFI**/**UEFI(Unified Extensible Firmware Interface)**

Up to this stage, Machine does not access any mass storage media. Thereafter, information on date,time, and most important peripherals are loaded from **CMOS**(**C**omplementary **M**etal **O**xide **S**emiconductor) values.

**CMOS** - `Technology used for the battery-powered memory store which allows the system to keep track of the date and time even when it is powered off`

A number of boot loaders exists for Linux

- **GRUB** (for **GR**and **U**nified **B**oot loader)
- **SOLINUX** (for booting from removable media)
- **DAS U-Boot** (for booting on embedded devices/appliances)

Most Linux boot Loaders can present a user interface for choosing alternative option for booting Linux, and even other operating systems that might be installed. 

When booting Linux, the boot loader is responsible for loading the kernel image and the initial Ram disk or filesystem(which contains some critical files and devices drivers needed to start into memory)

### Boot Loader in Action

**Two distinct stages**

Systems with **BIOS/MBR** method

- Boot loader resides at first sector of hard disk, also known as **M**aster **B**oot **R**ecord (**MBR**).
  - Size of MBR is 512 bytes
  - In this stage, boot loader examines the partition table and finds a bootable partition. Once if finds a bootable, it searches for second stage boot loader, GRUB, and loads it into RAM(Random Access Memory)

Systems with **EFI/UEFI ** method.

- UEFI firmware reads its Boot Manager data to determine which UEFI application is to be launched and from where(i.e from which disk and partition the EFI partition can be found)
- Firmware launches UEFI application, GRUB, as defined in boot entry in the firmware's boot manager. 
  - Procedure is more complicated, but more versatile compared to old MBR.
- Second Stage boot loader resides under /boot. A splash screen is displayed, allows us to choose which OS to boot.
  - After choosing OS, boot loader load kernel of the selected operating system into RAM and pass control to it.
  - Kernels are almost always compressed, so its first job is to uncompress itself.
  - After this, it will check and analyze system hardware and initialize any hardware device drivers built into the kernal.

### Initial Ram Disk

The **initramfs** filesystem image contains programs and binary files that perform all actions needed to mount the proper root filesystem, 

- Providing kernel functionality for the needed filesystem and device drivers for mass storage controllers with a facility called **udev** (for **u**ser **dev**ice), 
  - which is responsible for figuring out which devices are present, locating the device drivers they need to operate properly, and loading them. After the root filesystem has been found, it is checked for errors and mounted.

The **mount** program instructs the operating system that a filesystem is ready for use, and associates it with a particular point in the overall hierarchy of the filesystem (the **mount point**). If this is successful, the initramfs is cleared from RAM and the init program on the root filesystem (***\*/sbin/init\****) is executed.

**init handles the mounting and pivoting over to the final real root filesystem. If special hardware drivers are needed before the mass storage can be accessed, they must be in the initramfs image.**

### Login

Near the end of the boot process, **init** starts a number of text-mode login prompts. 

- Only on systems without a graphical login interface.

### Linux Kernal

The boot loader loads both the **kernel** and an initial RAM–based file system (initramfs) into memory, so it can be used directly by the kernel.  

When the kernel is loaded in RAM, it immediately initializes and configures the computer’s memory and also configures all the hardware attached to the system. This includes all processors, I/O subsystems, storage devices, etc. The kernel also loads some necessary user space applications.

## /sbin/init and Services

Once the kernel has set up all its hardware and mounted the root filesystem, 

- the kernel runs ***\*/sbin/init\****. This becomes the initial process, 
- which then starts other processes to get the system running. 
- Most other processes on the system trace their origin ultimately to **init**; exceptions include the so-called kernel processes. These are started by the kernel directly, and their job is to manage internal operating system details.

Besides starting the system, **init** is responsible for

-  keeping the system running and for shutting it down cleanly. 
- Act when necessary as a manager for all non-kernel processes; it cleans up after them upon completion, and restarts user login services as needed when users log in and out,
- The same for other background system services.

Traditionally, this process startup was done using conventions that date back to the 1980s and the System V variety of UNIX. This serial process has the system passing through a sequence of **runlevels** containing collections of scripts that start and stop services. Each runlevel supports a different mode of running the system. Within each runlevel, individual services can be set to run, or to be shut down if running.

However, all major recent distributions have moved away from this sequential runlevel method of system initialization, although they usually support the System V conventions for compatibility purposes. Next, we discuss the newer methods, **systemd** and **Upstart**.



## Startup Alternative

**SysVinit** viewed things as a serial process, divided into a series of sequential stages. Each stage required completion before the next could proceed. Thus, startup did not easily take advantage of the ***parallel processing\*** that could be done on multiple processors or cores.

Furthermore, shutdown and reboot was seen as a relatively rare event; exactly how long it took was not considered important. 

This is no longer true, especially with mobile devices and embedded Linux systems. Some modern methods, such as the use of **containers**, can require almost instantaneous startup times. Thus, systems now require methods with faster and enhanced capabilities. Finally, the older methods required rather complicated startup scripts, which were difficult to keep universal across distribution versions, kernel versions, architectures, and types of systems. The two main alternatives developed were:

**Upstart**

- - - Developed by Ubuntu and first included in 2006
    - Adopted in Fedora 9 (in 2008) and in RHEL 6 and its clones.

**systemd**

- - - Adopted by Fedora first (in 2011)
    - Adopted by RHEL 7 and SUSE 
    - Replaced Upstart in Ubuntu 16.04

# Systemd

- Systems with **systemd** start up faster than those with earlier **init** methods. 

- Replaces init that has serialized set of steps with aggressive parallelization techniques
  -  permits multiple services to be initiated simultaneously.

- Complicated startup shell scripts are replaced with simpler configuration files, 
  - Enumerate what has to be done before a service is started, how to execute service startup, and what conditions the service should indicate have been accomplished when startup is finished. 
  - Note : ***/sbin/init*** now points to **/lib/systemd/systemd**
    - As **systemd** took over the **init** process.



One **systemd** command (**systemctl**) is used for most basic tasks. 

- - - Starting, stopping, restarting a service (using **nfs** as an example) on a currently running system:
      ***\*$ sudo systemctl start|stop|restart nfs.service\****
    - Enabling or disabling a system service from starting up at system boot:
      ***\*$ sudo systemctl enable|disable nfs.service\****

In most cases, the ***\**\*.service\*\**\*** can be omitted.



 

## Linux File Systems

- Conventional disk filesystems: ext2, ext3, ext4, XFS, Btrfs, JFS, NTFS, etc.
- Flash storage filesystems: ubifs, JFFS2, YAFFS, etc.
- Database filesystems
- Special purpose filesystems: procfs, sysfs, tmpfs, squashfs, debugfs, etc.

## Partition and Filesystems

- Partition - Physically contiguous(next or together in sequence) section of disk, or what ppears to be in some advanced setup
- Filesystem is a method of storing/finding files on hard disk usually in a partition.

Partition can be thought of as a container which filesystem reside

`However in some circumstances, filesystem can span more than one partition using symbolic links`

|                                  | **Windows** | **Linux**              |
| -------------------------------- | ----------- | ---------------------- |
| Partition                        | Disk1       | **/dev/sda1**          |
| Filesystem Type                  | NTFS/VFAT   | EXT3/EXT4/XFS/BTRFS... |
| Mounting Parameters              | DriveLetter | MountPoint             |
| Base Folder (where OS is stored) | C:\         | /                      |

## Filesystem Hierarchy Standard

Linux systems store important files according to a standard layout called the **F**ilesystem **H**ierarchy **S**tandard (**FHS**), which has long been maintained by the Linux Foundation. Having a standard is designed to ensure that users, administrators, and developers can move between distributions without having to re-learn how the system is organized.

Linux uses the ‘/’ character to separate paths (unlike Windows, which uses ‘\’), and does not have drive letters. 

- Multiple drives and/or partitions are mounted as directories in the single filesystem. 
- Removable media such as USB drives and CDs and DVDs will show up as mounted at /run/media/yourusername/disklabel for recent Linux systems, or under /media for older distributions. 
- For example, if your username is student a USB pen drive labeled FEDORA might end up being found at /run/media/student/FEDORA, and a file README.txt on that disc would be at /run/media/student/FEDORA/README.txt.

# What is a process

A **process** is simply an instance of one or more related tasks (threads) executing on your computer

### Process Types

A terminal window (one kind of command shell) is a process that runs as long as needed. 

It allows users to execute programs and access resources in an interactive environment. You can also run programs in the background, which means they become detached from the shell.

| **Process Type**      | **Description**                                              | **Example**                        |
| --------------------- | ------------------------------------------------------------ | ---------------------------------- |
| Interactive Processes | Need to be started by a user, either at a command line or through a graphical interface such as an icon or a menu selection. | **bash, firefox, top**             |
| Batch Processes       | Automatic processes which are scheduled from and then disconnected from the terminal. These tasks are queued and work on a **FIFO** (**F**irst-**I**n, **F**irst-**O**ut) basis. | **updatedb, ldconfig**             |
| Daemons               | Server processes that run continuously. Many are launched during system startup and then wait for a user or system request indicating that their service is required. | **httpd, sshd, libvirtd**          |
| Threads               | Lightweight processes. These are tasks that run under the umbrella of a main process, sharing memory and other resources, but are scheduled and run by the system on an individual basis. An individual thread can end without terminating the whole process and a process can create new threads at any time. Many non-trivial programs are multi-threaded. | **firefox, gnome-terminal-server** |
| Kernel Threads        | Kernel tasks that users neither start nor terminate and have little control over. These may perform actions like moving a thread from one CPU to another, or making sure input/output operations to disk are completed. | **kthreadd, migration, ksoftirqd** |

### Process Scheduling and States

- **Schedular** - Critical kernel function
- Constantly shift processes on and off CPU, sharing time according to :
  - Relative priority
  - How much time is needed
  - How much has already been granted to a task

**Running** state - Either currently executing instructions on a CPU, or waiting to be granted a share of time( a time slice) so it can execute.

**Sleep state** - waiting for something to happen before they can resume, perhaps for user to type something.

**Terminating state** - Child process completes, parent process not asked about its state. It will be in a zombie state, not really alive but still show up in system's list of process.

### Process and Thread IDs

At any given time, there are always multiple processes being executed.

- The operating system keeps track of them by assigning each a unique process ID (**PID**) number. 

- The PID is used to track process state, CPU usage, memory use, precisely where resources are located in memory, and other characteristics.

- New PIDs are usually assigned in ascending order as processes are born. Thus, PID 1 denotes the **init** process (initialization process), and succeeding processes are gradually assigned higher numbers.

The table explains the PID types and their descriptions:

 

| **ID Type**              | **Description**                                              |
| ------------------------ | ------------------------------------------------------------ |
| Process ID (PID)         | Unique Process ID number                                     |
| Parent Process ID (PPID) | Process (Parent) that started this process. If the parent dies, the PPID will refer to an adoptive parent; on recent kernels, this is kthreadd which has PPID=2. |
| Thread ID (TID)          | Thread ID number. This is the same as the PID for single-threaded processes. For a multi-threaded process, each thread shares the same PID, but has a unique TID. |

### Terminating a Process

```bash
kill -SIGKILL <pid> kill -9 <PID> # can only kill your own process unless you are root.
```

## User and Group IDS

Many users can access a system simultaneously, and each user can run multiple processes.

- The operating system identifies the user who starts the process by the Real User ID (RUID) assigned to the user.

- The user who determines the access rights for the users is identified by the Effective UID (EUID). The EUID may or may not be the same as the RUID.

- Users can be categorized into various groups. 
  - Each group is identified by the Real Group ID (RGID). 
  - The access rights of the group are determined by the Effective Group ID (EGID). 
  - Each user can be a member of one or more groups.

Most of the time we ignore these details and just talk about the User ID (UID) and Group ID (GID).

## Priorities

At any given time, many processes are running (i.e. in the run queue) on the system. However, a CPU can actually accommodate only one task at a time, just like a car can have only one driver at a time. 

- Some processes are more important than others, so Linux allows you to set and manipulate process priority. 
- Higher priority processes grep preferential access to the CPU.

The priority for a process can be set by specifying a **nice value**, or niceness, for the process. 

- The lower the nice value, the higher the priority. 
- Low values are assigned to important processes, while high values are assigned to processes that can wait longer. 
- A process with a high nice value simply allows other processes to be executed first. 

In Linux, a nice value of **-20** represents the highest priority and **+19** represents the lowest. 

You can also assign a so-called **real-time priority** to time-sensitive tasks, such as controlling machines through a computer or collecting incoming data. This is just a very high priority and is not to be confused with what is called hard real-time which is conceptually different, and has more to do with making sure a job gets completed within a very well-defined time window.

### Renice to set Priorities

```bash
ps  # snapshot of current processes
ps lf # with full-format listing.
renice +5 3077 # lower priority of PID 3077
```

# Process Metrics and Control

## Load Averages

The **load average** is the average of the load number for a given period of time. It takes into account processes that are:

- - - Actively running on a CPU
    - Considered runnable, but waiting for a CPU to become available
    - Sleeping: i.e. waiting for some kind of resource (typically, I/O) to become available.

***Note***: Linux differs from other UNIX-like operating systems in that it includes the sleeping processes. Furthermore, it only includes so-called **uninterruptible** sleepers, those which cannot be awakened easily.

The load average can be viewed by running **w**, ***\*top\**** or **uptime**. We will explain the numbers on the next page.

Displayed using three numbers : 

```bash
load average: 0.45, 0.17, 0.12
```

- **0.45**: For the last minute the system has been 45% utilized on average.
- **0.17**: For the last 5 minutes utilization has been 17%.
- **0.12**: For the last 15 minutes utilization has been 12%.

If **Second Value is 1.00**, that would imply that the single-CPU system was 100% utilized, on average, over the past 5 minutes; this is good if we want to fully use a system. A value over **1.00** for a single-CPU system implies that the system was over-utilized: there were more processes needing CPU than CPU was available.

If we had more than one CPU, say a quad-CPU system, we would divide the load average numbers by the number of CPUs. In this case, for example, seeing a 1 minute load average of **4.00** implies that the system as a whole was 100% (4.00/4) utilized during the last minute.

## Background and Foreground Processes

- Foreground jobs run directly from the shell, and when one foreground job is running, other jobs need to wait for shell access (at least in that terminal window if using the GUI) until it is completed. 
  - This is fine when jobs complete quickly. But this can have an adverse effect if the current job is going to take a long time (even several hours) to complete.

- In such cases, you can run the job in the background and free the shell for other tasks. 
- The background job will be executed at lower priority, which, in turn, will allow smooth execution of the interactive tasks, and you can type other commands in the terminal window while the background job is running. By default, all jobs are executed in the foreground. You can put a job in the background by suffixing ***\*&\**** to the command, for example: ***\*updatedb &\****.

**CTRL-Z** to suspend a foreground job or **CTRL-C** to terminate a foreground job.

**bg**  to run a process in background and **fg** to run in foreground.

### Jobs

The **jobs** utility displays all jobs running in background. 

```bash
jobs 
[1] - Running                 Sleep 100 &
# display shows the job ID, state, and command name.
[1] - 1147 Running                 Sleep 100 &
jobs -l # provides the same information as jobs, and adds the PID of the background jobs.


```

## PS Commands (SystemV Style)

- provides information about currently running processes keyed by PID. 
- If you want a repetitive update of this status, you can use top or other commonly installed variants such as htop or atop from the command line, or invoke distribution's graphical system monitor application.

- ps has many options for specifying exactly which tasks to examine, what information to display about them, and precisely what output format should be used.

- Without options, ps will display all processes running under the current shell. 
  - -u option to display information of processes for a specified username.
  -  ps -ef displays all the processes in the system in full detail. 
  - ps -eLf displays one line of information for every thread (a process can contain multiple threads).

## PS Commands (BSD Style)

- Another style of option specification, 

  - Stems from the BSD variety of UNIX, where options are specified without preceding dashes. 
  - ps aux displays all processes of all users. 
  - ps axo allows you to specify which attributes you want to view.

  ```
  ps axo stat,priority,pid,pcpu,comm
  ```

  

## Process Tree

`pstree`-  displays the processes running on the system in the form of a tree diagram showing the relationship between a process and its parent process and any other processes that it created. Repeated entries of a process are not displayed, and threads are displayed in curly braces.

## top

**top** to get constant real-time updates (every two seconds by default), until you exit by typing **q**. 

**top** clearly highlights which processes are consuming the most CPU cycles and memory (using appropriate commands from within **top**).

 By default, processes are ordered by highest CPU usage. 

#### top Interactive keys

| **Command** | **Output**                                                |
| ----------- | --------------------------------------------------------- |
| t           | Display or hide summary information (rows 2 and 3)        |
| m           | Display or hide memory information (rows 4 and 5)         |
| A           | Sort the process list by top resource consumers           |
| r           | Renice (change the priority of) a specific processes      |
| k           | Kill a specific process                                   |
| f           | Enter the ***\*top\**** configuration screen              |
| o           | Interactively select a new sort order in the process list |

## Scheduling Future processes

`at` - utility program to execute any non-interactive command at a specified time.

```
$ at now + 2 days
cat file1.txt
<EOT>

```

# CRON

`cron` is a time-based scheduling utility program.

- It can launch routine background jobs at specific times and/or days on an on-going basis. 
- `cron` is driven by a configuration file called /etc/crontab (cron table)
  - which contains the various shell commands that need to be run at the properly scheduled times. There are both system-wide crontab files and individual user-based ones. 
  - Each line of a crontab file represents a job, and is composed of a so-called CRON expression, followed by a shell command to execute.

`crontab -e`  - open the crontab editor to edit existing jobs or to create new jobs. Each line will contain 6 fields:

| **Field** | **Description** | **Values**                |
| --------- | --------------- | ------------------------- |
| MIN       | Minutes         | 0 to 59                   |
| HOUR      | Hour field      | 0 to 23                   |
| DOM       | Day of Month    | 1-31                      |
| MON       | Month field     | 1-12                      |
| DOW       | Day Of Week     | 0-6 (0 = Sunday)          |
| CMD       | Command         | Any command to be execute |

##### Cron Example

```bash
 * * * * * /usr/local/bin/execute/this/script.sh
# Schedule a job to execute script.sh every minute of every hour of every day of the month, and every month and every day in the week.
30 08 10 06 * /home/sysadmin/full-backup 
# Schedule a full-backup at 8.30 a.m., 10-June, irrespective of the day of the week.
```

##### Scheduling a Periodic task with `Cron`

```bash
# mycrontab

0 10 * * * /tmp/myjob.sh
      
#/ tmp/myjob.sh
#!/bin/bash
echo Hello I am running $0 at $(date)
      
$ chmod +x /tmp/myjob.sh # Make it executable
      
$ crontab mycrontab # Add to crontab system
      
$ crontab -l      # Verify loaded
0 10 * * * /tmp/myjob.sh
$ sudo ls -l /var/spool/cron/user
-rw------- 1 user user 34 Apr 22 09:59 /var/spool/cron/user
$ sudo cat /var/spool/cron/user
0 10 * * * /tmp/myjob.sh

$ crontab -r # to remove job
```



## Sleep

`sleep` suspends execution for at least the specified period of time,

- which can be given as the number of seconds (the default), minutes, hours, or days. 
- After that time has passed (or an interrupting signal has been received), execution will resume.

```bash
sleep NUMBER[SUFFIX]...

where SUFFIX may be:

s for seconds (the default)
m for minutes
h for hours
d for days.
```

**Note: sleep and at are quite different; sleep delays execution for a specific period, while at starts execution at a later time.**

##### Using `at` for Batch Processing in the Future

```bash
#!/bin/bash
date > /tmp/datestamp
         
$ chmod +x testat.sh
$ at now + 1 minute -f testat.sh
         
$ atq # See if the job is queued up to run with atq:
Thu Apr 16 16:34:37 +08 2020 a test
      
$ cat /tmp/datestamp # check if job ran
         
         
$ at now + 1 minute     # Add job to queue
at> date > /tmp/datestamp
CTRL-D

$ atq
```

## 