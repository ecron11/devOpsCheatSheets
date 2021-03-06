# RHCSA Cheat sheet
## A Cheat sheet for linux command and concepts related to the RHCSA certification as of July 31st, 2021.
If you're here and reading this, you can do it! And this can help. I believe in you and the power of good note-taking.  
This cheat sheet is organized by exam objectives and subobjectives. Each sub-objective has relevant notes underneath with commands and topics related to it. Good luck!

## Objectives

### Understand and use essential tools (Todo)
#### Access a shell prompt and issue commands with correct syntax (Todo)
```bash
find -iname <name>
```
Finds a file by name case-insensitive
#### Use input-output redirection (>, >>, |, 2>, etc.)
- \> redirects output to a file, overwrites the files
- \>> Appents output to a file
- 2> redirects standard error output to a file
- 2>> Appends standard error output to a file
- | pipes output into another command
- Using backticks ` in quotes lets you use variables

#### Use grep and regular expressions to analyze text (Todo)

#### Access remote systems using SSH
```bash
ssh user@remote command
```
You can execute commands on a remote server without connecting by specifying command after ssh

#### Log in and switch users in multiuser targets
```bash
su - <user>
```
Switches your user. If user is left out it will assume root.

```bash
sudo -i
```
Switches you to root user if you have sudo access

```bash
sudo su - user
```
Lets you switch to a user whose password you don't know if you have sudo access

#### Archive, compress, unpack, and uncompress files using tar, star, gzip, and bzip2
**Archiving vs. Compressing**
Archiving is collecting multiple files together, compressing is reducing the size of a file.  
tar and star are examples of common archiving utilities
gzip and bzip2 and examples of compression utilities
Archiving and compressing can be used together or seperately.

```bash
tar cvf <archivename> <files to be archived>
```
Creates an archive file from the file/directories specified. The three flags used are:
- c create
- v verbose. Give a lot of feedback
- f file

```bash
star -cv file=<archivename> <file/directories to be archived>
```
The equivalent command with star

```bash
tar tvf <archive>
```
Lists the contents of an archive. The flag t is used to list the contents

```bash
star -cv file=<archiven>
```
The equivalent command with star

```bash
tar cvfz <archivename> <files to archive>
```
Uses the z flag to compress with gzip inline

```bash
star -cv -bz file=<archivename> <files to be archivied>
```
Creates an archive and compresses it with bzip2 using star

```bash
tar xvfj <archivename>
```
Decompresses and extracts an archive. The x flag is to extract, and the j flag is for bzip2.

```bash
bzip2 -d <filename>
```
Decompresses a file using the -d flag

#### Create and edit text files

##### vim commands
- dd deletes a line
- x deletes a character. Can specify a number to delete a number of characters
- r and then a character replaces a character
- u to undo
- :w saves. Adding a space and a name will save a copy with that name
- O puts a line above, lowercase o puts a line below
- c w deletes the rest of the word
- ~ changes the case of a character
- A puts you into insert mode at the end of a line
- ZZ saves and exits a file
- gg takes you to the top of the file
- G takes you to the bottom 
- typing a number and then G will take you to that line number
- a number plus % will take you to that percent of the file
- w takes you forward through a file, word by word instead of character
- b takes you back
- a number then w will take you that manay words forward
- search with /
- n will take you to the next find in a search, and N will take you to the last
- ? will search backwards. Using n will take you backwards and N will take you forwards
- shift+6 takes you to the first non-blank character of a line
- 0 takes you to the first charU4nxwL]S
- can pair number with paste to paste multiple times
- V puts you in visual line mode
- J joins lines together
- u undos changes
- ctrl + r redoes changes
- :h gets help

```bash
vim +<command>
```
opens vim and runs a commands immediately, such as a find

```bash
vim +<number>
```
Takes straight to a line

#### Create, delete, copy, and move files and directories
```bash
mkdir -p <directory_name>
```
Makes a directory. -p creates the parents as well

```bash
tree directory
```
Shows the directory structure of a directory Might need sudo for some restricted directories

```bash
2> /dev/null
```
Discards errors from a command

```bash
journalctl --unit=<service> --no-pager >> file
``` 
journalctl is a command for getting the log files collected from systemd. --no-pager prevents it from using a pager which is good for redirection

```bash
wc -l file
```
Counts the number of lines in a text file

#### Create hard and soft links

##### Hard link vs. Soft Link
U4nxwL]S 
Can't cross filesystem boundaries  
Same inode number and permissions   

**Soft Link**
Special file that points to another file  
Can be created for directories  
Can cross filesystem boundaries and partitions  
Different inode number and file permissions  
Doesn't contain file data  

```bash
ln <file/dir> <linkname>
```
Creates a hard link to a file/directory. Add -s to create a soft link
#### List, set, and change standard ugo/rwx permissions (Todo)
#### Locate, read, and use system documentation including man, info, and files in /usr/share/doc
```bash
man <command>
```
Brings up the manual pages for a command or service

```bash
info <command>
```
Brings up the info pages for command or service

```bash
whatis <command>
```
Returns the man page number and description of command

```bash
apropos <keyword>
```
Like whatis, but more info with keyword matches

```bash
ls -la /usr/share/doc | egrep -i "command"
```
Finds help documents related to a command in usr/share/doc

### Create simple shell scripts (Todo)
#### Conditionally execute code (use of: if, test, [], etc.) (Todo)
test can be used to tests files. test -e checks if a file exists and test -d checks if a variable is a directory
#### Use Looping constructs (for, etc.) to process file, command line input (Todo)
#### Process script inputs ($1, $2, etc.) (Todo)
#### Processing output of shell commands within a script (Todo)
#### Processing shell command exit codes (Todo)
$? Contains the exit code of the last run command

### Operate running systems (Todo)
#### Boot, reboot, and shut down a system normally (Todo)



#### Boot systems into different targets manually (Todo)
##### Linux boot targets

```bash
systemctl get-default
```
Gets the current deault target

```bash
systemctl set-default <target>
```
Sets the default target

```bash
sudo systemctl reboot
```
reboots the system



#### Interrupt the boot process in order to gain access to a system (Todo)
To interrupt the boot process press e.  
To boot into emergency mode, add rd.break in GRUB loader at end of linux line

##### Changing root password you don't know
1. Boot into emergency mode
   1. To do this, reboot the system and press e to get into grub loader
   2. Add rd.break in the line with linux in it. ctrl+e will get you to the end of the line
   3. ctrl + x to Boot
2. Remount /sysroot into readwrite
   1. Run 'mount -o rw,remount /sysroot'
3. change the root to /sysroot
   1. chroot /sysroot
4. Change the admin password with passwd root
5. Force SElinux to relabel or else the system won't Boot
   1. Just touch ./autorelabel
6. Exit /sysroot
7. Remount sysroot as readonly
   1. mount -o ro,remount /sysroot
8. exit and restart

##### The boot process
1. The server/VM is powered on and the BIOS loads and executes POST
2. BIOS loads the contents of the MBR (Master boot record
3. The Grub bootloader loads the kernel
4. The kernel loads drivers and starts systemd
5. systemd reads etc/systemd configuration files and default.target file
6. system is brought to state as defined by default.target

The GRUB bootloader let's you edit your boot options 


#### Identify CPU/memory intensive processes and kill processes (Todo)
```bash
top
```
Showing CPU usage and other statistics of running processes

```bash
ps -ef
```
Shows running processes. -e is for all running processes and f lists them in long form

```bash
pgrep <pattern>
```
Does a grep of running processes. A shortcut for ps and grep

```bash
pkill <process name>
```
Kills a process by name

#### Adjust process scheduling (Todo)
```bash
chrt -f -p <priority level> <process>  
```
let's you set the priority and scheduling algorithm for a process. -f is for fifo

##### Nice values
Nice values are a priority that a process runs with. They range from -20 being the highest priority to 19 being the lowest.  
To set a niceness value from the beginning, run the nice command:
```bash
nice -n 19 <command>
```
Can change the nice value with the renice command

#### Manage tuning profiles (Todo)
##### Tuned
Tuned is a utility to optomize system performance

```bash
tuned-adm active
```
Shows the active profile. tuned-adm is how you manage tuned.

```bash
tuned-adm profile <profile name>
```
Sets a profile

```bash
tuned-adm recommend
```
Recommeds a profile

```bash
tuned-adm profile <profile1> <profile2>
```
Will attempt to merge the 2 profiles  

###### Dyanmic Tuning
To enable dynamic tuning, must edit /etc/tuned/tuned-main.conf

#### Locate and interpret system log files and journals (Todo)
##### Different log locations
- /var/logs/ The old school location for system and application logs
- journalctl is the utility to query the systemd journal
- systemd-journald must be configured to log to disk to make sure that they persist across reboots

```bash
journalctl -u chronyd
```
Finds the journal for chronyd. -u stands for unit, which is how you specify your search  
-g will let you do patern matching
#### Preserve system journals (Todo)
To make journals persistent you must edit the Storage setting in /etc/systemd/journald.conf or if it is set to auto, make the directory /var/log/journal  
After creating the /var/log/journal directory, running a journalctl --flush  
- By default, it is set to auto, which will log to /var/log/journal IF it exists already. If not it will log to /run/log/journal which is not persistent.
- Volatile will log to run/log/journal,
- Persistent will log to var/log/journal which is persistent.
- None will just discard any logs it receives.

#### Start, stop, and check the status of network services (Todo)
#### Securely transfer files between systems

```bash
scp user@remote:path/to/file local/dest/path
```
Lets you copy a file over ssh from a remote to local destination

```bash
sftp user@remote
```
Lets you use sftp to copy files.
mget used to get files

### Configure local storage (Todo)
#### List, create, delete partitions on MBR and GPT disks (Todo)
##### MBR vs. GPT
MBR (Master Boot Record) is the older way used to partition tables, with limitations compared to GPT. These include a max of 4 primary partitions and a max partition size of 2 Terabytes. This is opposed to GPT (Globally Uinique Disk Identifier Partition Table) which can have up to 128 primary partitions with 2^33 terabytes maximum partition size.

##### fdisk vs. gdisk
fdisk is used only for MBR partition tables and gdisk is for GPT Partition tables.

```bash
fdisk -l
```
displays partitions on all disks

```bash
gdisk <disk>
```
Opens the gdisk util on specified disk

```bash
fdisk <disk>
```
Opens fdisk util on specified disk

```bash
mkfs.ext4 <partition>
```
Makes a ext4 filesystem on a partition

```bash
mount <partition/filesystem> <directory>
```
Mounts a filesystem/partition onto a directory.

#### Create and remove physical volumes
##### Logical Volume Management
Logical Volume Managenment (LVM) is an alternative to the traditional method of partitioning and mounting filesystems. It is composed of 3 pieces:
1. Physical Volumes: These are the building blocks of LVM. These are the partitions or disks that are used to make shared storage pools.
2. Volume Groups: These are groups of physical Volumes. This creates a shared pool of storage that can be divided up into logical volumes.
3. Logical Volumes: These are the filesystems that are inevitably created. They are allocated from Volume Groups and can be mounted to directories.

```bash
pvs
```
List physical volumes

```bash
pvcreate <disk_Or_partition>
```
Creates a physical volume from a disk or partition

#### Assign physical volumes to volume groups (Todo)
```bash
vgs
```
List volume groups

```bash
vgcreate <vg_name> <physical_volume>
```
Creates a volume group with a physical volume

#### Create and delete logical volumes (Todo)
```bash
lvs
```
Shows Logical Volumes

##### logical volume location
Logical volumes are located in the /dev/mapper directory. Their names are <volume_group>-<logical_volume_name>

#### Configure systems to mount file systems at boot by universally unique ID (UUID) or label
##### Persistent Mounts
Mounts that are configured to mount automatically, either at boot time or when a request to mount all filesystems is issued. This is important to ensure that systems are configured to survive routine processes such as reboot. 

**Adding mounts to /etc/fstab ensures persistence. This is very important for the exam**  

##### Persistent Unique Identifiers
To add something to be mounted persistently, you need a unique identifier that doesn't change. There are 2 types of identifiers: File System Identifiers and Device Identifiers.

###### File Systems Identifiers
Identifies a file system on a block device. Options are File System UUID and File system label

###### Device Identifiers
Identifies a block device. Options are a WWID, partition UUID, and Device Serial Number

```bash
lsblk
```
Lists block devices/mounts

```bash
umount <directory>
```
Unmounts a filesystem from a directory

```bash
mount -a
```
Mounts everything in fstab

```bash
blkid
```
Lists the block Ids including UUID of the devices in the system

##### Making a filesystem mount persistent
To make a mount persistent follow thse steps:
1. Get the UUID of the partition/filesystem with blkid
2. Add the UUID with the directory to /etc/fstab
3. Test with umount <directory> and mount -a

##### Making a swap mount persistent
Making a swap mount persistent is very similar to making a filesystem mount persistent. To make a swap mount persistent follow thse steps:
1. Get the UUID of the partition/filesystem with blkid
2. Add the UUID to /etc/fstab. Instead of a directory, write swap
3. Test with swapon -a. This will mount anything in the /etc/fstab

#### Add new partitions and logical volumes, and swap to a system non-destructively
##### Swap Space
Swap space is space on a hard disk that is used basically as backup for memory. If a system has used all of it's memory, but needs more, it'll put less used parts of memory (chunks of memory are divided into pages) into swap space, freeing up some memory. The combination of swap space and RAM is called virtual memory.

```bash
swapon
```
shows how much swap space is configured  

```bash
mkswap -L <swap_label_name> <partition>
```
Adds a partition to swap space with a label

```bash
swapon <swap_partition>
```
Activates swap space

##### Adding more swap space
To Add more swap space:
1. Create a partition with swap type
2. Add partition to swap space with mkswap
3. Activate new swapspace with swapon


##### Making a swap file
1. Create a file, initializing it with null values from /dev/zero.
2. Use swapon command on the file
3. Change permissions to 600 as recommended by swapon command

### Create and configure file systems (Todo)
#### Create, mount, unmount, and use vfat, ext4, and xfs file systems (Todo)
##### Comparison of xfs, vfat, and ext4

###### xfs
- For very large files and file systems
- Large systems running enterprise workloads
- Parallel workloads
- The default for RHEL8
- Can grow file system online, but can't shrink
- 1 PiB max

###### ext4
- For smaller file systems and files
- For systems with limited io
- For CPU-bound workloads
- Latest version of ext
- Can grow and shrink file system
- Max size, 50 TiB

###### vfat
- Works with many operating systems
- Great for sharing files across different operating systems
- Supports windows long file names
- Max size of 16 TiB
#### Mount and unmount network file systems using NFS (Todo)
#### Extend existing logical volumes (Todo)
#### Create and configure set-GID directories for collaboration (Todo)
#### Configure disk compression (Todo)
#### Manage layered storage (Todo)
#### Diagnose and correct file permission problems (Todo)

### Deploy, configure, and maintain systems (Todo)
#### Schedule tasks using at and cron (Todo)
#### Start and stop services and configure services to start automatically at boot (Todo)
#### Configure systems to boot into a specific target automatically (Todo)
#### Configure time service clients (Todo)
#### Install and update software packages from Red Hat Network, a remote repository, or from the local file system (Todo)
#### Work with package module streams (Todo)
#### Modify the system bootloader (Todo)

### Manage basic networking (Todo)
#### Configure IPv4 and IPv6 addresses (Todo)
#### Configure hostname resolution (Todo)
#### Configure network services to start automatically at boot (Todo)
#### Restrict network access using firewall-cmd/firewall (Todo)

### Manage users and groups (Todo)
#### Create, delete, and modify local user accounts (Todo)
#### Change passwords and adjust password aging for local user accounts (Todo)
#### Create, delete, and modify local groups and group memberships (Todo)
#### Configure superuser access(Todo)

### Manage security (Todo)
#### Configure firewall settings using firewall-cmd/firewalld (Todo)
#### Create and use file access control lists (Todo)
#### Configure key-based authentication for SSH (Todo)

##### Configure passwordless ssh login
- Switch to user you want to configure passwordless login for
- Generate a ssh key with ssh-keygen
- Use ssh-copy-id for user on the other server

##### ssh-agent
ssh-agent is a helper program that keeps track of your keys/logins. You can run ssh-add to add ssh-keys on the system to it. The use of this is so that if they have a passphrase, you won't have to enter it everytime you ssh in. Only when running the ssh-add command. This allows sso login even with a ssh-key file that has a passphrase

#### Set enforcing and permissive modes for SELinux (Todo)
#### List and identify SELinux file and process context (Todo)
#### Restore default file contexts (Todo)
#### Use boolean settings to modify system SELinux settings (Todo)
#### Diagnose and address routine SELinux policy violations (Todo)

### Manage containers (Todo)
#### Find and retrieve container images from a remote registry (Todo)
#### Inspect container images (Todo)
#### Perform container management using commands such as podman and skopeo (Todo)
#### Perform basic container management such as running, starting, stopping, and listing running containers (Todo)
#### Run a service inside a container (Todo)
#### Configure a container to start automatically as a systemd service (Todo)
#### Attach persistent storage to a container (Todo)

## miscellaneous commands and concepts

```bash
sort <file>
```
Sorts a file's lines. Can add -n for numerical sort  

- {} are used for parameter substition. For instance in executing a command within the find command  
- !! is used to represent the last command executed

```bash
screen
```
screen let's you create multiple screens in a terminal session

### /dev/zero
/dev/zero provides a stream of null characters. Can be used to initialize an empty file