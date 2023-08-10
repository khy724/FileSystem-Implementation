# FileSystem-Implementation
Implemented a basic file system which utilises an emulated disk whose interface matches raw hardware abstraction

The disk of the emufs filesystem is emulated using text files on disk. We call these
files mount points and store these (mounted) mount points in an array of mount_t
struct.
The emulated disk consists of at most 64 blocks of size 256 bytes each. The first block
of the filesystem is the superblock, which contains a summary of the status of all
other blocks in the system as well as summary of all the inodes. The next 2 blocks of
the disk store inodes, with 16 inodes per block. That is, the filesystem can store a
total of 32 entities (files & directories) on disk. The remaining blocks are available to
store file data.
The inode structure is 16 bytes in size, enabling the filesystem to pack 16 inodes into
a disk block. There are only 4 mappings allowing only 1024 bytes per file and 4
entities per directory. Inode 0 is the inode for the root directory.

You can find the description of the following structs in emufs-disk.h:
* superblock_t
* inode_t
* metadata_t
* mount_t

* struct mount_t* opendevice(char* device_name, int size) opens and sets up a
device for emulation via a file (called device_name). It also attaches the opened device to one of the available mount points. A mount point emulates a
pathname to disk mapping.
When a device/disk is opened for the first time the function creates a file to
emulate the disk and initializes the super block on the disk with device_name,
disk_size, and magic_number.
* int readblock(int dev_fd, int blocknum, char* buf) emulates reading a block
from disk, identified by the device number (file descriptor) dev_fd and a
memory region (buf). The size of the buffer is assumed to be one block.
* int writeblock(int dev_fd, int blocknum, char* buf) emulates writing a memory
buffer to a block. The size of the buffer is assumed to be one block.
* void closedevice(struct mount_t* mount_point) closes the device (file used to
emulate the device) and removes device from the corresponding mount point.
When a device is closed, further accesses to the files/directories stored on the
device are not allowed so the corresponding entries in directory and file
handles are also closed.
* void mount_dump() prints information about the mounted devices.
* void read_superblock(int mount_point, struct superblock_t* superblock)
reads the superblock of the device into the provided buffer.
* void write_superblock(int mount_point, struct superblock_t* superblock)
updates the superblock of the device with the provided buffer.

All operations that write to the disk will make changes to the emulated disk (text file)
before returning. It is currently assumed that only one process modifies the text file
(emulated disk) at any time. Emulation code does not handle any concurrency
when editing the emulated disk file.

* int create_file_system(int mount_point, int fs_number) sets up the file system
emufs on the opened disk attached to the mount point. Fs-number represents a
file system, e.g., 0 is emufs with non-encrypted content and 1 is emufs with
encrypted content.
* void fsdump(int mount_point) displays details from the metadata block
regarding the directory structure on the disk attached to a mount point. For
output format refer to sample outputs.

### Encrypted File System
We store the datablocks and metadata in encrypted form as defined in the encrypt function in
emufs-disk.c. And we retrieve the data by decryption using the decrypt function.
When you create or mount an encrypted emufs, you’ll be prompted to provide a key
that’ll be used for decryption and encryption.
All the blocks except the superblock are encrypted. Only the magic number in the
superblock is encrypted


