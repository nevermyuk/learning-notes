# Linux Foundation Certified SysAdmin(LFCS)

## Essential Commands

### [find](http://man7.org/linux/man-pages/man1/find.1.html)

- Used to search for file

  ```bash
   # / to start from root directory.
  find / -name "example.txt" 
  #find all directories
  find / -type d
  # -iname flag for case insensitive
  find /foo -iname "example.txt" # search in /foo for example.txt
  #  find all file ending with .log
  find / -type f -name "*.log"
  # find file by file size
  find / -size  +1M
  # find file created less than a day ago
  find / -mtype 1
  # find file by permission
  find / -perm 755
  # find file with 755 permission and change to 700.
  find / -perm 755 -exec chmod 700 '{}' \;
  ```

### locate

- less powerful than find and uses database to search for results

```bash
locate test.txt
```

### df

- report free disk space

  ```bash
  # -h flag for human readable format.
  df -h
  ```

### which

- find out where program is installed

  ```bash
  # python location
  which python 
  ```

### format

- simple text formatter

  ```bash
  format -u format.txt
  ```

### nl

- add line numbers

  ```bash
  nl combinedlist.txt
  ```

### cut

- cut files

  ```bash
  #delimited.txt
  This is a;delimited file
  cut -d ";" -f2 delimited.txt
  #output
  delimited file
  ```

  

## Basic File Systems

TL;DR : Ext4 File System is used for Production Linux.

### What is a File system?

A file system is where a computer system persists general data for users or/and applications. A organizational system to retrieve data from block device.

A block device is a set of addressable blocks used to store and retrieve data.

e.g. SSD, Magnetic Hard Drives, USB flash storage

File systems can affect:

- Performance of system
- Efficiency of media
- Compatibility with other systems

#### What is Journaling?

- Designed to prevent data corruption from crash or power loss

- Adds overhead to file writes

- High performance servers might not need it.

- Not used on removable media such as removable flash drives.

- Introduced in ext3

  File write starts > metadata/inode of file transfer recorded on journal >

  ​	> File written > work complete is recorded on journal

#### Ext1-4

- Extended File System

#### BtrFS

- ext4 replacement
- B-Tree File System
- Drive pooling, snapshots, compression, online defragmentation.

#### ReiserFS

- Lots of feature not implemented by Ext.
- Unlikely to continue development

#### ZFS

- Drive pooling,snapshots, dynamic disk striping
- Each file has checksum

#### XFS

- Similar to Ext4
- Can be enlarged(but not shrunk) on the fly
- Good with large files(backup servers)
- Poor with many small file(web servers)

#### JFS

- Journaled File System
- Low CPU
- Good performance regardless file size
- Partitions can be dynamically enlarged but not shrunk
- Not as widely tested in production as Ext4

#### Swap

- Not an actual file system , Virtual memory
- Scratch space for stuff that cannot fit in RAM
- Hibernating
- Comparable to window paging file

#### FAT

- FAT16,FAt32,exFAT
- Microsoft 'File Allocation Table' file system
- Not journaled
- Great for USB drives on Windows or Apple.

### Output Redirection

#### Pipe   | 

- Directing output from one command to the input of another

```bash
cat example.txt | less | sort
```

#### Greater than  > 

- Direct output into program or file

```bash
cat examplelist1.txt catlist2.txt | sort > combinedlist.txt
```

#### >>

- Append to file

```bash
cat examplelist3.txt >> combinedlist.txt
```

#### Lesser than <

- Direct input into program or file

```bash
cat < blah.txt 
```

## Regular Expression

- Analyze text using basic regular regular exp
- Search and replace

#### Star * 

- Wild card, match any character.

#### Carat ^

- Anchor to front of line if in Regex expression

- **If inside square brackets, negate.**

  ```bash
  [^0-9] # returns flle that is not 0-9
  ```

#### Dollar  $

- Anchor to end of line

#### Square Bracket [ ]

- Include characters to find or a range.

  ```bash
  [AaBbCc][0-9][A-Z][a-z][aeiou]
  ```

#### Dot .

- match any character except line break

#### Backslash \

- escape character

#### Brackets ( )

- create subgroups that allows to capture and select.

- maximum 9

  ```bash
  # match 2 lowercase letter
  grep '\([a-z]\)\1' example.txt 
  ```

### Quantifiers

#### Asterisk *

- 0 or more times - {0,} 

#### Plus +

- Match 1 or more times - {1,}

####  Question Mark ?

- 0 or none {0,1}

#### Curly Brackets { n , m }  - Min max

```
{3} - Match 3 times - {n}
{2,5} - Match 2 to 5 times 
{3,} - Match 3 or more times 
{,3} - Match maximum 3 times
```

