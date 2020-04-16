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

# Mount and Unmounting

`mount`  is used to attach a filesystem (which can be local to the computer or on a network) somewhere within the filesystem tree. The basic arguments are the **device node** and mount point. 

```bash
$ sudo mount /dev/sda5 /home 
# attach fs contained in disk partition associated with the /dev/sda5 device node, into the filesystem tree at the /home mount point.
$ sudo umount /home # umount
```

**If you want it to be automatically available every time the system starts up,** 

- Edit **/etc/fstab** accordingly (the name is short for filesystem table). 
- Looking at this file will show you the configuration of all pre-configured filesystems. 
- **man fstab** will display how this file is used and how to configure it.

Executing **mount** without any arguments will show all presently mounted filesystems.

 `df -Th` (disk-free) 

- display information about mounted filesystems,

-  including the filesystem type, and usage statistics about currently used and available space.

## NFS and Network Filesystems

It is often necessary to share data across physical systems which may be either in the same location or anywhere that can be reached by the Internet. A network (also sometimes called distributed) filesystem may have all its data on one machine or have it spread out on more than one network node. A variety of different filesystems can be used locally on the individual machines; a network filesystem can be thought of as a grouping of lower level filesystems of varying types.

System administrators mount remote users' home directories on a server in order to give them access to the same files and configuration files across multiple client systems. 

- This allows the users to log in to different computers, yet still have access to the same files and resources.

The most common such filesystem is named simply **NFS** (the **N**etwork **F**ile**s**ystem). 

Another Type for Microsoft is **CIFS** (Common Internet File System)(also termed **SAMBA**).

### NFS on the SERVER

NFS uses daemons and other system servers.

```bash
$ sudo systemctl start nfs
```

The text file `/etc/export`contains the directories and permissions that a host is willing to share with other systems over NFS. An entry in this file may look like the following:

```bash
/projects *.example.com(rw) # allows the directory /projects to be mounted using NFS with read and write (rw) permissions and shared with other hosts in the example.com domain.
```

After modifying the `/etc/exports`file

```bash
sudo exportfs -av # to notify linux about directories allowed to be remotely mounted using NFS.
```

Alternatively, restart NFS

```bash
$ sudo systemctl restart nfs # heavier and halt NFS before starting it up again
$ sudo systemctl enable nfs
```

**Note**: On RHEL/CentOS 8**,** the service is called **nfs-server**, not **nfs**).

### NFS on the CLIENT

On client machine, to mount remote filesystem automatically upon system boot.

**Modify** `/etc/fstab` , an example entry follows:

```bash
# /etc/fstab

servername:/projects /mnt/nfs/projects nfs defaults 0 0
```

**Note: **you may want to use the **nofail** option in **fstab** in case the NFS server is not live at boot.

Or alternatively, mount without a reboot, or as a one time mount

```bash
$ sudo mount servername:/projects /mnt/nfs/projects
```

### Showing Filesystems mounted

```bash
cat /etc/fstab
mount
cat /proc/mounts
```

# Overview of User Directories

Each user has a home directory under `/home`

Root user has `/root`

### /bin and /sbin

`/bin` - contain executable binaries, essential commands used to boot system or in single-user mode, and essential commands by all system users, such as cat,cp,ls,mv,ps,rm.

`/sbin` - contain essential binaries related to system administration, such as **fsck and ip**.

`/usr/bin or /usr/sbin` - Traditionally for non-traditional commands. 

- Newest linux distro usually symbolically linked
  -  `/usr/bin` and `/bin` together.
  -  `/usr/sbin` and `/sbin` together.

### /proc filesystem

**Pseudo-filesystems** - no permanent presence anywhere on disk.

The `/proc` filesystem is very useful because the information it reports is gathered only as needed and never needs storage on the disk.

`/proc` contains virtual files ( files that exist only in memory) that permits viewing constantly changing kernel data.

- Files and directories that mimic kernel structure and config info.
- No real files, but runtime system informations
  - e.g system memory, device mounted, hardware configuration,

```bash
/proc/cpuinfo
/proc/interrupts
/proc/meminfo
/proc/mounts
/proc/partitions
/proc/version
# Shows there is a directory for every process running on the system containing vital information.

/proc has subdirectories as well,

/proc/<Process-ID-#>
/proc/sys
# Shows avirtual directory that contains a lot of information about the entire system, in particular its hardware and configuration

```

### /dev

`/dev` - contains **device nodes** - a type of pseudo-file used by most hardware and software devices, except for network devices.

- Empty on the disk partition when it is not mounted
- Contains entries which are created by the `udev`(device manager for linux kernel) system, which creates and manages device nodes on Linux, creating them dynamically when devices are found. The /dev directory contains items such as:
  - /dev/sda1 (first partition on the first hard disk)
  - /dev/lp1 (second printer)
  - /dev/random (a source of random numbers).

### /var - variable

`var` - contains files that are expected to change insize and content as the system is running

```bash
System log files: /var/log
Packages and database files: /var/lib
Print queues: /var/spool
Temporary files: /var/tmp.
```

- The `/var` directory may be put on its own filesystem so that growth of the files can be accommodated and any exploding  file sizes do not fatally affect the system. 

- Network services directories such as /var/ftp (the FTP service) and /var/www (the HTTP web service) are also found under /var.

### /etc

`etc` - home for system configuration file

- It contains no binary programs, although there are some executable scripts. 
- For example, `/etc/resolv.conf` tells the system where to go on the network to obtain host name to IP address mappings (DNS). 
- **Files like passwd, shadow and group for managing user accounts are found in the /etc directory.** 
- While some distributions have historically had their own extensive infrastructure under /etc 
  -  Red Hat and SUSE have used /etc/sysconfig.

**Note that /etc is for system-wide configuration files and only the superuser can modify files there. User-specific configuration files are always found under their home directory.**

### /boot

`boot` - contains few essential files needed to boot the system.

- For every alternative kernel installed on the system there are four files:

- vmlinuz - The compressed Linux kernel, required for booting.
- initramfs - The initial ram filesystem, required for booting, sometimes called initrd, not initramfs.
- config - The kernel configuration file, only used for debugging and bookkeeping.
- System.map - Kernel symbol table, only used for debugging.

**GRUB (Grand Unified bootloader) files such as `/boot/grub/grub.conf` or `/boot/grub2/grub2.cfg`** are found here.

### /lib and /lib64

`lib` contains 32 bit libraries (common code shared by applications and needed for them to run) for essential programs in `/bin` and `/sbin`

- Library filenames either start with `ld` or `lib`
  - e.g `/lib/libncurses.so.5.9`

Most are known as dynamically loaded libraries/shared libraries or Shared objects(SO).

`lib64` for 64-bit libraries.

**On recent Linux distributions, just like for /bin and /sbin, the directories just point to those under /usr.**

**Kernel modules (kernel code, often device drivers, that can be loaded and unloaded without re-starting the system) are located in /lib/modules/<kernel-version-number>. **

### /media, /run and /mnt - Removable Media

- Removable media, such as USB drives, CDs and DVDs. 

- To make the material accessible through the regular filesystem, it has to be mounted at a convenient location. 

- Most Linux systems are configured so any removable media are automatically mounted when the system notices something has been plugged in.

Historically this was done under the **/media** directory, 

- Modern Linux distributions place these mount points under the **/run** directory. 
  - AUSB pen drive with a label ***\*myusbdrive\**** for a user name `user` would be mounted at **/run/media/student/myusbdrive**.

`/mnt`directory has been used since the early days of UNIX for temporarily mounting filesystems. 

- These can be those on removable media, but more often might be network filesystems , which are not normally mounted. 
- Or these can be temporary partitions, or so-called **loopback** filesystems, which are files which pretend to be partitions.

### Additional Directories Under /:

| **Directory Name ** | **Usage**                                                    |
| ------------------- | ------------------------------------------------------------ |
| /opt                | Optional application software packages                       |
| /sys                | Virtual pseudo-filesystem giving information about the system and the hardware             Can be used to alter system parameters and for debugging purposes |
| /srv                | Site-specific data served up by the system                                                                           Seldom used |
| /tmp                | Temporary files; on some distributions erased across a reboot and/or may actually be a ramdisk in memory |
| /usr                | Multi-user applications, utilities and data                  |

### /usr Directory tree

`/usr` contains non-essential programs and scripts

| **Directory Name ** | **Usage**                                                    |
| ------------------- | ------------------------------------------------------------ |
| /usr/include        | Header files used to compile applications                    |
| /usr/lib            | Libraries for programs in `usr/bin` and `/usr/sbin`          |
| /usr/lib64          | 64-bit libraries for 64-bit programs in `/usr/bin`and `/usr/sbin` |
| /usr/sbin           | Non-essential system binaries, such as system daemons        |
| /usr/share          | Shared data used by applications, generally architecture-independent |
| /usr/src            | Source code, usually for the Linux kernel                    |
| /usr/local          | Data and programs specific to the local machine. Subdirectories include `bin`, `sbin`, `lib`, `share` ,`include` ,`etc`. |
| /usr/bin            | This is the primary directory of executable commands on the system |



# Managing Files and Directories

### Diff

`diff` is used to compare files and directories.

**diff** is meant to be used for text files; for binary files, use `cmp`

| **diff Option** | **Usage**                                                    |
| --------------- | ------------------------------------------------------------ |
| -c              | Provides a listing of differences that include three lines of context before and after the lines differing in content |
| -r              | Used to recursively compare subdirectories, as well as the current directory |
| -i              | Ignore the case of letters                                   |
| -w              | Ignore differences in spaces and tabs (white space)          |
| -q              | Be quiet: only report if files are different without listing the differences |

```
 diff [options] <filename1> <filename2>
```

### diff3 and patch

#### diff3

`diff3` compare 3 files at once. 

- One file as reference for the other two
  - e.g you and another developer made modification to the same file working at the same time independently
- diff3 can show the differences based on the common file you both started with

```bash
$ diff3 MY-FILE COMMON-FILE YOUR-FILE
```

#### patch

Many modifications to source code and configuration files are distributed utilizing patches with the **patch** program. 

A `patch file` contains the deltas (changes) required to update an older version of a file to the new one. The patch files are actually produced by running ***\*diff\**** with the correct options:

```bash
$ diff -Nur originalfile newfile > patchfile
```

- Distribution of patch is more concise and efficient than distributing entire file.

```bash
# applying a patch
$ patch -p1 < patchfile
$ patch originalfile patchfile
```

### Using diff and patch

```bash
$ cd /tmp
$ cp /etc/group /tmp
$ dd if=/tmp/group of=/tmp/GROUP conv=ucase
1+1 records in
1+1 records out
751 bytes copied, 0.0013365 s, 562 kB/s
$ diff -Nur group GROUP > patchfile
$ cat patchfile
--- group       2020-04-16 17:43:33.845071800 +0800
+++ GROUP       2020-04-16 17:43:46.234982200 +0800
@@ -1,55 +1,55 @@
-root:x:0:
-daemon:x:1:
-bin:x:2:
-sys:x:3:
...
+NETDEV:X:114:USER
+USER:X:1000:
+DOCKER:X:999:USER
...
$ patch --dry-run group patchfile
checking file group
$ patch group patchfile # same as $ patch group < patchfile  or $ patch < patchfile
patching file group
diff group GROUP
```



## file Utility

In Linux, a file's extension often does not categorize it might in other OS.

- Cannot assume file.txt is a text file and not an executable program.
- Filename is more meaningful to user than the system

`file` is used to ascertained the real nature of the file.

- For the file names given as arguments,
-  it examines the contents and certain characteristics to determine whether the files are plain text, shared libraries, executable programs, scripts, or something else.

# Backing Up Data

`rsync` - more efficient, checks if file copied already exist. Copy only parts of file that have actually changed.

- can also be used to copy file from one machine to another
- Location designated in` target:path`, where `target` can be in the form of `someone@host`.
- Efficient when recursively copying one directory tree to another. 
  - Due to only differences transmitted over network.
  - Often used to synchronize destination directory tree with origin.
  - -r option to recursively walk down directory tree copying all files and directories below the one listed as source.

`cp ` - copy files to and from destination on local machines.

### rsync

`rsync` is a very powerful utility. 

```bash
$ rsync sourcefile destinationfile
$ rsync -r project-X archive-machine:archives/project-X
#  back up project directory
$ rsync --progress -avrxH  --delete sourcedir destdir
```

**Note: Always use -dry-run option to ensure that it is what you want**

## Compressing data

| **Command** | **Usage**                                                    |
| ----------- | ------------------------------------------------------------ |
| gzip        | The most frequently used Linux compression utility           |
| bzip2       | Produces files significantly smaller than those produced by **gzip** |
| xz          | The most space-efficient compression utility used in Linux   |
| zip         | Is often required to examine and decompress archives from other operating systems |

`tar` is often used to group files in an archive then compress the whole archive at once

### gzip

`gzip` - most often used Linux compression utility. Compress well and fast

```bash
$ gzip *	 # Compresses all files in the current directory; each file is compressed and renamed with a .gz extension
$ zgip -r projectX	 # Compresses all files in the projectX directory, along with all files in all of the directories under projectX.
$ gunzip foo # De-compresses foo found in the file foo.gz. 
# Under the hood, the gunzip command is actually the same as gzip –d

```

### bzip2

`bzip2` - different compression algorithm, produce significantly smaller files at the price of compression speed. Used for larger files

```bash
$ bzip2 * # Compresses all of the files in the current directory and replaces each file with a file renamed with a .bz2 extension
$ bunzip2 *.bz2 # Decompresses all of the files with an extension of .bz2 in the current directory. 
# Under the hood, bunzip2 is the same as calling bzip2 -d
```

### xz

`xz` most space  efficient compression utility used in Linux. used to store archive of Linux Kernel. Trades slower compression speed for an eve nhigher compression ratio

```bash
$ xz * # Compresses all of the files in the current directory and replaces each file with one with a .xz extension
$ xz foo # Compresses foo into foo.xz using the default compression level (-6), and removes foo if compression succeeds
$ xz -dk bar.xz	# Decompresses bar.xz into bar and does not remove bar.xz even if decompression is successful
$ xz -dcf a.txt b.txt.xz > abcd.txt # Decompresses a mix of compressed and uncompressed files to standard output, using a single command
$ xz -d *.xz Decompresses the files compressed using xz
```

## Handling Files using Zip

`zip` not often used to compressed files in Linux, but used to decompress archive from other OS.

```bash
$ zip backup * # Compresses all files in the current directory and places them in the backup.zip
$ zip -r backup.zip ~ # Archives your login directory (~) and all files and directories under it in backup.zip
$ unzip backup.zip	# Extracts all files in backup.zip and places them in the current directory

```



## Archiving and Compressing Data using Tar

`tar` - **t**ape **ar**chive , originally used to archive fil eto magnetic tape. 

- Allows you to create or extract files from an archive, called **tarball**
- Optionally compress while creating and decompress when extracting.

```bash
$ tar xvf mydir.tar	# Extract all the files in mydir.tar into the mydir directory
$ tar zcvf mydir.tar.gz mydir	# Create the archive and compress with gzip
$ tar jcvf mydir.tar.bz2 mydir	# Create the archive and compress with bz2
$ tar Jcvf mydir.tar.xz mydir	# Create the archive and compress with xz
$ tar xvf mydir.tar.gz	# Extract all the files in mydir.tar.gz into the mydir directory
```

Note: You do not have to tell tar it is in gzip format



### Archiving the Home Directory

```bash
$ tar -cvf /tmp/backup.tar ~ # same as tar -cvf /tmp/backup.tar /home/user

# 3 Types of compression
$ tar zcf /tmp/backup.tar.gz ~
$ tar jcf /tmp/backup.tar.bz2 ~
$ tar Jcf /tmp/backup.tar.xz ~
# Comparision
$ ls -lh /tmp/backup* # -h to make it human readable.

```

**xz** has the best compression, followed by **bz2** and then **gz**.

## Disk-to-Disk copying (dd)

`dd` is useful for making copies of raw disk space.

- Back up Master Boot Record (MBR) - first 512 byte sector on disk that contains a table describing partition on that disks

  ```bash
  dd if=/dev/sda of=sda.mbr bs=512 count=1
  # to make a copy of one disk onto another, will delete everything that previously existed on the second disk.
  
  ```

  **DO NOT EXPERIMENT WITH THE COMMAND IF YOU DO NOT KNOW WHAT YOU ARE DOING**

# User Environment

`whoami` - identify current user

`who` - list current logged-on users

### Order of Startup File

When first login, /etc/profile is read and evaluated followed by : 

1. ~/.bash_profile
2. ~/.bash_login
3. ~/.profile 

## Basics of Users and Groups

`users` are assigned a unique user ID(uid), an iteger; normal users start with uid 1000 or greater.

`groups` - **groups** for organizing users. 

- Groups are collections of accounts with certain shared permissions. 

- Control of group membership is administered through the **/etc/group** file, which shows a list of groups and their members. 

- By default, every user belongs to a default or primary group. 
  - When a user logs in, the group membership is set for their primary group and all the members enjoy the same level of access and privilege. 

- Permissions on various files and directories can be modified at the group level.

Users also have one or more group IDs (**gid**), including a default one which is the same as the user ID. 

- These numbers are associated with names through the files ***\*/etc/passwd\**** and ***\*/etc/group\****. 
- Groups are used to establish a set of users who have common interests for the purposes of access rights, privileges, and security considerations. 
- Access rights to files (and devices) are granted on the basis of the user and the group they belong to.

### Adding and Removing Users

```bash
$ sudo useradd testuser
$ sudo /usr/sbin/useradd testuser # OpenSUSE
# by default, sets the home directory to /home/testuser, populates it with some basic files (copied from /etc/skel) and adds a line to /etc/passwd such as:
testuser:x:1002:1002::/home/testuser:/bin/bash

$ userdel testuser # remove user but leave home directory intact
$ userdel -r testuser # for full removal.

$ id # info about current user
uid=1000(user) gid=1000(user) groups=1000(user),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),108(lxd),114(netdev),999(docker)


```

### Adding and Removing Groups

```bash
$ sudo /usr/sbin/groupadd anewgroup

$ sudo /usr/sbin/groupdel anewgroup # remove group

$ groups testuser
testuser : user docker 

$ sudo /usr/sbin/usermod -a -G anewgroup testuser # Adding a user to an already existing group is done with usermod. 

$ groups testuser
rjsquirrel: user docker anewgroup

# These utilities update /etc/group as necessary. 
# Make sure to use the -a option, for append, so as to avoid removing already existing groups. 
# groupmod can be used to change group properties, such as the Group ID (gid) with the -g option or its name with then -n option.

# Removing a user from the group is somewhat trickier. The -G option to usermod must give a complete list of groups. 

$ sudo /usr/sbin/usermod -G anewgroup testuser

$ groups testuser
rjsquirrel : user anewgroup


```

## The root Account

`root` - powerful and full acess to system , superuser.

- **sudo** for limited privilege to user account
  - temporary
  - Only for a specific subset of commands

### su and sudo

`su` - switch or substitute user to launch a new shell running as another user. Do not use unless necessary.

`sudo` - preferred.

### Elevating to root account

**sudo** configuration files are stored in the **/etc/sudoers** file and in the **/etc/sudoers.d/** directory. By default, the **sudoers.d** directory is empty.

## Environment Variables

View the values of currently set environment variables. Use **set**, **env**, or ***\*export\**.**

### Setting Environment Variables

By default, variables created within a script are only available to the current shell

- child processes (sub-shells) will not have access to values that have been set or modified. 
- Allowing child processes to see the values requires use of the **export** command.

```bash
$ echo $shell # Show the value of a specific variable	
$ export VARIABLE=value # Export a new variable value	
$ VARIABLE=value; export VARIABLE # Export a new variable value	

# Adding a variable permanently
# Edit ~/.bashrc and add the line 
export VARIABLE=value
# source ~/.bashrc or just (dot). ~/.bashrc or just open a new bash

# Set environment variables to be fed as a one shot to a command
SDIRS=s_0* KROOT=/lib/modules/$(uname -r)/build make modules_install
# feeds the values of the SDIRS and KROOT environment variables to the command make modules_install.
```

### HOME variable

- represents home/login directory of user
- cd without argument.
- ~ is abbreviation for $HOME.
  - cd $HOME and cd ~ is the same.

#### PATH variable

- ordered list of directories(the path)
- Scanned when a command is given to find the appropriate program or script to run. 
  - Each directory in the path is separated by colons (***\*:\****). 
  - A null (empty) directory name (or ***\*./\****) indicates the current directory at any given time.

```bash
:path1:path2 # null directory before the first colon (:).
 path1::path2 # null directory between path1 and path2. 

 # To prefix a private bin directory to your path:
 
 $ export PATH=$HOME/bin:$PATH
```

#### Shell Variable

- points to user's default command shell
- Usually bash

```bash
$ echo $SHELL
/bin/bash
```

## PS1 Variable and the Command Line Prompt

`Prompt Statement (**PS**) `is used to customize prompt string in  terminal windows to display the information you want.

`PS1` is the primary prompt variable which controls what your command line prompt looks like. The following special characters can be included in `PS1:`

```bash
\u - User name
\h - Host name
\w - Current working directory
\! - History number of this command
\d - Date
```

### history

`history` -  show previously entered commands in history buffer.

- stored in ~/.bash_history. 

  ```bash
  HISTFILE
  The location of the history file. 
  HISTFILESIZE
  The maximum number of lines in the history file (default 500). 
  HISTSIZE 
  The maximum number of commands in the history file. 
  HISTCONTROL
  How commands are stored. 
  HISTIGNORE
  Which command lines can be unsaved.
  
  
  ```

`!!` - bang bang to execute previous command

`CTRL-R`  to search previous commands.

```
!	Start a history substitution
!$	Refer to the last argument in a line
!n	Refer to the nth command line
!string	Refer to the most recent command starting with string
```

## Keyboard Shortcuts

```
Keyboard Shortcut	Task
CTRL-L	Clears the screen
CTRL-D	Exits the current shell
CTRL-Z	Puts the current process into suspended background
CTRL-C	Kills the current process
CTRL-H	Works the same as backspace
CTRL-A	Goes to the beginning of the line
CTRL-W	Deletes the word before the cursor
CTRL-U	Deletes from beginning of line to cursor position
CTRL-E	Goes to the end of the line
Tab	Auto-completes files, directories, and binaries
```

# File Ownership

Every file is associated with a user, who is the owner

File is also associated with a group which has an interest in the file and certain rights/permissions

Read,Write,Execute

```
chown	Used to change user ownership of a file or directory
chgrp	Used to change group ownership
chmod	Used to change the permissions on the file, which can be done separately for owner, group and the rest of the world (often named as other)
```

## File Permission Modes and chmod

Files have three kinds of permissions: read (r), write (w), execute (x). 

 These permissions affect three groups of owners: user/owner (u), group (g), and others (o).

```bash
rwx: rwx: rwx
 u:   g:   o
$ ls -l somefile
-rw-rw-r-- 1 student student 1601 Mar 9 15:04 somefile
$ chmod uo+x,g-w somefile
$ ls -l somefile
-rwxr--r-x 1 student student 1601 Mar 9 15:04 somefile
```

- 4 - read permission 
- 2 - write permission 
- 1 - execute permission 
- 7 - read/write/execute
- 6 - read/write
- 5 - read/execute.

```bash
$ chmod 755 somefile # 3 digits , RWX for User, RX for Group and Others.
```

### Chown

`chown` - for changing file ownership

```bash
$ chown user file
$ chown user:group file # change both owner and group at the same time
```

### Chgrp

`chgrp` - for changing group ownership

```bash
$ chgroup group file
```

# Manipulating Text

## Cat 

`cat` for concatenate , often used to print,read and view file content

```bash
$ cat <filename>
```

`tac` - print file in reverse order

```bash
$ tac <filename>
```



```bash
$ cat file1 file2	# Concatenate multiple files and display the output; 
# Entire $ content of the first file is followed by that of the second file
$ cat file1 file2 > newfile	# Combine multiple files and save the output into a new file
$ cat file >> existingfile	# Append a file to the end of an existing file
$ cat > file	# Any subsequent lines typed will go into the file, until CTRL-D is typed
$ cat >> file	# Any subsequent lines are appended to the file, until CTRL-D is typed

$ cat << EOF > somefile
> Anything will go into file
> Yes it will!
> Last line
> EOF
```

## Working with large Files

```bash
$ less somefile
$ cat somefile | less
```

### head

`head` - print first file line of file

### tail

`tail` - print last few line of file

### Viewing Compressed file

- Can only use version of programs designed to work directly with compressed files

```bash
$ zcat compressed-file.txt.gz	# To view a compressed file
$ zless somefile.gz
or
$ zmore somefile.gz	# To page through a compressed file
$ zgrep -i less somefile.gz	# To search inside a compressed file
$ zdiff file1.txt.gz file2.txt.gz	# To compare two compressed files
```

## sed

sed is a powerful text processing tool and is one of the oldest, earliest and most popular UNIX utilities.

-  It is used to modify the contents of a file or input stream, 
- usually placing the contents into a new file or output stream.
-  Abbreviation for **S**tream **Ed**itor 
- Can filter text, as well as perform substitutions in data streams.

Data from an input source/file (or stream) is taken and moved to a working space. The entire list of operations/modifications is applied over the data in the working space and the final contents are moved to the standard output space (or stream).

```bash
$ sed -e command <filename>
# Specify editing commands at the command line, operate on file and put the output on standard out (e.g. the terminal)
$ sed -f scriptfile <filename>	
# Specify a scriptfile containing sed commands, operate on file and put output on standard out
```

#### sed basic operations

```bash
$ sed s/pattern/replace_string/ file	# Substitute first string occurrence in every line
$ sed s/pattern/replace_string/g file	# Substitute all string occurrences in every line
$ sed 1,3s/pattern/replace_string/g file	
# Substitute all string occurrences in a range of lines
$ sed -i s/pattern/replace_string/g file	
# Save changes for string substitution in the same file
```

## awk

awk is used to extract and then print specific contents of a file and is often used to construct reports.

-  It was created at Bell Labs in the 1970s
- Derived its name from the last names of its authors: Alfred **A**ho, Peter **W**einberger, and Brian **K**ernighan.

- Is a powerful utility and interpreted programming language.
- Is used to manipulate data files, retrieving, and processing text.
- It works well with fields (containing a single piece of data, essentially a column) and records (a collection of fields, essentially a line in a file).

```bash
awk ‘command’  file	# Specify a command directly at the command line
awk -f scriptfile file	# Specify a file that contains the script to be executed
```

#### awk basic operations

```bash
awk '{ print $0 }' /etc/passwd	# Print entire file
awk -F: '{ print $1 }' /etc/passwd	# Print first field (column) of every line, separated by a space
awk -F: '{ print $1 $7 }' /etc/passwd	# Print first and seventh field of every line
```

#### Parsing with awk, sort and uniq

```bash
$ awk -F: '{print $7}' /etc/passwd | sort -u
$ awk -F: '{print $7}' /etc/passwd | sort | uniq
```



## File Manipulation Utility

#### sort 

`sort` is used to rearrange the lines of a text file, in either ascending or descending order according to a sort key.

```bash
$ sort <filename>	 # Sort the lines in the specified file, according to the characters at the beginning of each line
$ cat file1 file2 | sort	# Combine the two files, then sort the lines and display the output on the terminal
$ sort -r <filename>	# Sort the lines in reverse order
$ sort -k 3 <filename>	# Sort the lines by the 3rd field on each line instead of the beginning
$ sort -u <filename> # check for  unique values after sorting the records (lines) same as uniq
```

#### uniq

`uniq` removes duplicate consecutive lines in text file and useful for simplifying text display

- Requires duplicate entries to be consecutive
- **Must run sort first and pipe output into uniq**
- use sort with -u flag instead to do it in one step.

```bash
$ sort file1 file2 | uniq > file3  # remove duplicate entries from multiple files at once
$ sort -u file1 file2 > file3 # same as previous

$ uniq -c filename # count number of duplicate

```

#### paste

`paste`  can be used to combine fields (such as name or phone number) from different files, as well as combine lines from multiple files

- Different columns can be identified based on delimiters (spacing used to separate two fields).
- Delimiters can be a blank space, a tab, or an **Enter**. 

paste accepts 

- -d delimiters, which specify a list of delimiters to be used instead of tabs for separating consecutive values on a single line. 
  - Each delimiter is used in turn; when the list has been exhausted, paste begins again at the first delimiter.
- -s, which causes paste to append the data in series rather than in parallel; that is, in a horizontal rather than vertical fashion.

```bash
$ paste file1 file2
$ paste -d, file1 file2
$ paste -d ':' phone names
```

#### join

`join` - used to join files without repeating data of common columns.

- It first checks whether the files share common fields, such as names or phone numbers, and then joins the lines in two files based on a common field.

```bash
$  join file1 file2
```

#### split

`split` used to break up (or split) a file into equal-sized segments for easier viewing and manipulation, and is generally used only on relatively large files.

- By default, **split** breaks up a file into 1000-line segments. 
- The original file remains unchanged, and a set of new files with the same name plus an added prefix is created. 
- By default, the **x** prefix is added. 

```bash
$ split infile <Prefix>
$ wc -l american-english # split american-english dictionary 
$ split american-english dictionary # split into 100 equal sized segname named dictionaryx
```

## Regular Expression and Search Patterns

`Regular Expression`  are text strings used for matching a specific pattern, or to search for a specific location, such as the start or end of a line or a word. Regular expressions can contain both normal characters or so-called meta-characters, such as `*` and `$`.

- Text editor and utilities such as `vi`,`sed`,`awk`,`grep` work extensively with RegEx including Programming Languages.

```
.(dot)	Match any single character
a|z	Match a or z
$	Match end of string
^	Match beginning of string
*	Match preceding item 0 or more times


a..	matches azy
b.|j.	matches both br and ju
..$	matches og
l.*	matches lazy dog
l.*y	matches lazy
the.*	matches the whole sentence
```

#### grep

`grep` is extensively used as text searching tool. Scans file for specified patterns and used with RegEx and simple strings.



```bash
$ grep [pattern] <filename>	#Search for a pattern in a file and print all matching lines
$ grep -v [pattern] <filename>	# Print all lines that do not match the pattern
$ grep [0-9] <filename>	# Print the lines that contain the numbers 0 through 9
$ grep -C 3 [pattern] <filename>	
# Print context of lines (specified number of lines above and below the pattern) for matching the pattern. Here, the number of lines is specified as 3
```

#### using grep

```bash
$ grep user /etc/passwd # find username 
$ grep ftp /etc/services # find all entries with ftp
$ grep ftp /etc/services | grep tcp # restrict to tcp
$ grep -n ftp /etc/services | grep -v tcp # restrict to those that do not use tcp and print line number
$ grep -e ^ts -e st$ /etc/services # get all strings that start with ts or end with st
```



#### strings

`strings` used to extract al printable character strings found in file or files given as arguments.

useful in locating human-readable content embedded in binary file; **for text files can just use grep.**

```bash
$ strings book1.xls | grep my_string # search for my_string in spreadsheet
```

#### tr

`tr` to translate specified character into other character or delete them

```bash
$ tr [options] set1 [set2]
$ tr abcdefghijklmnopqrstuvwxyz ABCDEFGHIJKLMNOPQRSTUVWXYZ	
# Convert lower case to upper case
$ tr '{}' '()' < inputfile > outputfile	
# Translate braces into parenthesis
$ echo "This is for testing" | tr [:space:] '\t'	
# Translate white-space to tabs
$ echo "This   is   for    testing" | tr -s [:space:] 
# Squeeze repetition of characters using -s
$ echo "the geek stuff" | tr -d 't'	
# Delete specified characters using -d option
$ echo "my username is 432234" | tr -cd [:digit:]	
# Complement the sets using -c option
$ tr -cd [:print:] < file.txt	
# Remove all non-printable character from a file
$ tr -s '\n' ' ' < file.txt	
# Join all the lines in a file into a single line
$ cat cities | tr a-z A-Z
# Transform lowercase to uppercase

```

#### tee

`tee` take output of any command , while sending it to STDOUT , also save to file.

- tees the output stream from command: one stream is displayed on STDOUT and other is saved to file.

```bash 
$ ls -l | tee newfile
# list the contents of a directory on the screen and save the output to a file
$ ls -l /etc | tee /tmp/ls-output

```

#### wc

`wc` counts numbers of lines,words,characters in a file or list of files.

```bash
–l	Displays the number of lines
-c	Displays the number of bytes
-w	Displays the number of words
$ wc -l 
$ wc /var/log/*.log # all files in /var/log that has .log extension
```

#### cut

`cut` used for manipulating column-based file and is designed to extract specific columns.

- default column separator is the tab character
- different delimiter can be given as command option

```bash
$ ls -l | cut -d" " -f3 # display third column delimited by blank space
```

