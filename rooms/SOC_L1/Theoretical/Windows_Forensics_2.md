We learned about Windows Forensics in the [[Windows Forensics 1]] and practiced extracting forensic artifacts from the Windows Registry. We learned about gathering system information, user information, files and folders accessed, programs run, and external devices connected to the system, all from the Windows registry.

## The FAT file systems

File systems is a type of organization of bits into meaningful information. The main purpose of it to interpret these collections more easily and fast.

#### The File Allocation Table (FAT):

is one of these systems. It has been the default file system for Microsoft Operating Systems since at least the late 1970s and is still in use. It creates a table that indexes the location of bits that are allocated to different files.

#### Data structures of the FAT file system:

##### Clusters

A cluster is a basic storage unit of the FAT file system. Each file store on device can be considered a group of clusters containing bits of information

##### Directory

A directory contains information about file identification, like file name, starting cluster and filename length.

##### File Allocation Table

A linked list of all the clusters. It contains the status of the cluster and the pointer to the next cluster in the chain. The bits that make up a file are stored in clusters. All the filenames on a file system, their starting clusters, lengths are stored in directories. And the location of each cluster on the disk is stored in the FAT.

#### FAT12, FAT16, FAT32

The FAT file format divides the available disk space into clusters for more straightforward addressing. The number of these clusters depends on the number of bits used to address the cluster. The initial cluster size was 8-bit, later the storage needed to be increased, FAT12, FAT16 and FAT32 were introduced.

The following summarizes the information as mentioned earlier and how it impacts the maximum volume and file sizes:

- FAT32
  - 12 Addressable bits
  - 4096 maximal number of clusters
  - 512B-8KB of Supported size of clusters
  - 32MB maximum Volume Size
- FAT16
  - 16 Addressable bits
  - 65536 max number of clusters
  - 2KB-32KB of supported size of clusters
  - 2GB Maximum Volume Size
- FAT32 - 28 Addressable bits - 268,435,456 max number of clusters - 4KB-32KB supported size of clusters - 2TB Maximum Volume Size
  FAT16 and FAT32 are still used in some places like USB drives SD cards or Digital cameras, although maximum file size of 4 GB reduced the use of these systems

#### The exFAT file system

default for SD cards larger than 32GB. Maximum file size of 128PB and supports cluster size of 4KB to 32MB. It was created through pressure from manufactures towards Windows after introducing NTFS file system.

#### Questions:

1. How many addressable bits are there in the FAT32 file system?
   **Answer:** 28 bits
2. What is the maximum file size supported by the FAT32 file system? (In GB)
   **Answer:** 4GB
3. Which file system is used by digital cameras and SD cards?
   **Answer:** exFAT

## The NTFS File System

New Technology File System (NTFS offers more in terms of reliability, security and recovery capabilities. It also has some limitation when it comes to file and volume sizes. It introduces a lot of features that will be explained here.

#### Journaling

The NTFS File System keeps a log of changes to the metadata in the volume. This feature helps the system recover from a crash or data movement due to defragmentation. This log is stored in $LOGFILE in the volume's root directory

#### Access Controls

It has access controls that define the owner of file or directory and permissions for each user

#### Volume Shadow Copy

The NTFS system introduced Volume Shadow Copies to track changes made to a file. Using this feature a user can restore previous file versions for recovery or system restore.

#### Alternate Data Streams

A file is a stream of data organized in a file system. ADS is a feature that allows files to have multiple streams of data stored in a single file. Internet Explorer and other browsers use ADS to identify files downloaded from the Internet.

#### Master File Table

It's a structured database that tracks the objects stored in a volume. All data in NTFS is organized in this table

#### $MFT

It's the first record in the volume. The Volume Boot Record points to the cluster where it located. $MFT stores information about the clusters where all other objects present on the volume are located.

#### $LOGFILE

Stores the transactional logging of the file system

#### $UsnJrnl

Update Sequence Number (USN) Journal. It is present in the $Extend record. In contains information about all the files that were changed in the file system and the reason for the change.

#### MFT Explorer

is one of EZtools used to explore MFT files. It is available in both command line and GUI versions

```
MFTECmd.exe -f <path-to-$MFT-file> --csv <path-to-save-results-in-csv>
```

#### Questions

1. Parse the $MFT file placed in `C:\users\THM-4n6\Desktop\triage\C\` and analyze it. What is the Size of the file located at `.\Windows\Security\logs\SceSetupLog.etl`
   **Answer:** 49152
2. What is the size of the cluster for the volume from which this triage was taken?
   **Answer:** 4096

## Recovering deleted files

#### Disk Image:

A disk image file is a file that contains a bit-by-bit copy of a disk drive. A bit-by-bit copy saves all the data in a disk image file, including the metadata, in a single file. Thus, while performing forensics, one can make several copies of the physical evidence, i.e., the disk, and use them for investigation. This helps in two ways. 1) The original evidence is not contaminated while performing forensics, and 2) The disk image file can be copied to another disk and analyzed without using specialized hardware. We can perform it with [[Autopsy]]. After supplying a disk image file you can see files with a cross on them. Nevertheless you can extract them by using `Extract File` option.

#### Questions:

1. There is another xlsx file that was deleted. What is the full name of that file?
   **Answer:** Tryhackme.xlsx
2. What is the name of the TXT file that was deleted from the disk?
   **Answer:** TryHackMe2.txt
3. Recover the TXT file from Question #2. What was written in this txt file?
   **Answer:** thm-4n6-2-4

## Evidence of Execution

#### Windows Prefetch files

When a program is run in Windows, it store its information for future use. This stored information is used to load the program quickly in case of frequent use. The information is stored in Prefetch files which are located in `C:/Windows/Prefetch` directory. They have an extension of `.pf`. They contain the last run times of the application, the number of times the application was run and any files and device handles used by the file

For the analysis we can used PECmd.exe from the EZtools.

```
PECmd.exe -f <path-to-Prefetch-files> --csv <path-to-save-csv>
```

or

```
PECmd.exe -d <path-to-Prefetch-directory> --csv <path-to-save-csv>
```

#### Windows 10 Timeline

Windows 10 stores recently used applications and files in an SQLite database called the WIndows 10 Timeline. This data can be a source of information about the last executed programs. It contains the application that was executed and the focus time of the application. The Windows 10 timeline can be found at the following location:
`C:\Users\<username>\AppData\Local\ConnectedDevicesPlatform\{randomfolder}\ActivitiesCache.db`

We can use WxTCmd.exe to parse Windows 10 Timeline.

```
WxTCmd.exe -f <path-to-timeline-file> --csv <path-to-save-csv>
```

#### Windows Jump Lists

Windows introduced jump lists to help users go directly to their recently used files from the taskbar. This data is stored in the following directory:
`C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations`
We can use JLECmd.exe to parse this lists.

```
JLECmd.exe -f <path-to-Jumplist-file> --csv <path-to-save-csv>
```

#### Questions:

1. How many times was gkape.exe executed?
   **Answer:** 2
2. What is the last execution time of gkape.exe
   **Answer:** 12/01/2021 13:04
3. When Notepad.exe was opened on 11/30/2021 at 10:56, how long did it remain in focus?
   **Answer:** 00:00:41
4. What program was used to open C:\Users\THM-4n6\Desktop\KAPE\KAPE\ChangeLog.txt?
   **Answer:** Notepad.exe

## File/folder knowledge

#### Shortcut Files

Windows creates a shortcut file for each file opened either locally or remotely. The shortcut files contain information about the first and last opened times of the file and the path of the opened file, along with some other data. Shortcut files can be found in the following locations:
`C:\Users\<username>\AppData\Roaming\Microsoft\Windows\Recent\`

`C:\Users\<username>\AppData\Roaming\Microsoft\Office\Recent\`
We can use Eric Zimmerman's LECmd.exe (Lnk Explorer) to parse Shortcut files. When we run the LECmd.exe, we see the following options:

```
LECmd.exe -f <path-to-shortcut-files> --csv <path-to-save-csv>
```

#### IE/Edge history

We can check it using [[Autopsy]]. The files/folders accessed appear with a file:///\* prefix in the IE/Edge history.

#### Jump Lists

also jump lists could be used to achieve that.

#### Questions:

1.  When was the folder C:\Users\THM-4n6\Desktop\regripper last opened?
    **Answer:** 12/1/2021 13:01
2.  When was the above-mentioned folder first opened?
    **Answer:** 12/1/2021 12:31

## External Devices/USB device forensics

#### Setupapi dev logs for USB devices

When any new device is attached to a system, information related to the setup of that device is stored in the `setupapi.dev.log`. This log is present at the following location:
`C:\Windows\inf\setupapi.dev.log`
This log contains the device serial number and the first/last times when the device was connected.

#### Questions:

1. Which artifact will tell us the first and last connection times of a removable drive?
   **Answer:** Setupapi.dev.log

## Conclusion

Check [[Windows Forensics 1]] and [[KAPE]] room.
