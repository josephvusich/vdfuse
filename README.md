vdfuse
======

Virtualbox VDI files fuse mount tool. From https://sourceforge.net/projects/xspect3d/files/gordstuff/

This repository is forked from https://github.com/Thorsten-Sick/vdfuse, with modifications based on https://serverfault.com/a/1000645, plus instructions for manually modifying the VirtualBox headers to compile on MacOS Catalina.

Building for Mac
================

1. Install the latest [FUSE for macOS](https://osxfuse.github.io/). During installation ensure the "MacFUSE compatibility layer" is selected.
2. Install the latest [VirtualBox](https://www.virtualbox.org/).
3. Clone this repository and `cd` into the local checkout.
4. Run `svn co -r '{20120801}' http://www.virtualbox.org/svn/vbox/trunk/include/`
5. Modify `include/iprt/cdefs.h` and insert a new line after line 1792 containing `#undef PVM`
6. Run `./vdbuild-osx`

Mounting VHDs on Mac
====================

1. `sudo ./vdfuse -tVHD -w -f/Path/To/VHD /Path/To/Mountpoint`
2. `hdiutil attach -imagekey diskimage-class=CRawDiskImage -nomount /Path/To/Mountpoint/PartitionN`
3. `mount -t YourFS /dev/diskK /Path/To/ParitionMountPoint`

Building
========

You will need the virtualbox sourcecode. Please load the package from their homepage

bash vdbuild_new /path/to/virtualbox/source/include/ vdfuse-v82a.c

FUSE setup
==========

If you want to mount VMs as a default user, you will have to edit /etc/fuse.conf and add "user_allow_other" to the config file

Using it
========

./vdfuse -h
DESCRIPTION: This Fuse module uses the VirtualBox access library to open a 
VirtualBox supported VD image file and mount it as a Fuse file system.  The
mount point contains a flat directory containing the files EntireDisk,
Partition1 .. PartitionN.  These can then be loop mounted to access the
underlying file systems
Version: 0.83

USAGE: ./vdfuse [options] -f image-file mountpoint
	-h	help
	-r	readonly
	-t	specify type (VDI, VMDK, VHD, or raw; default: auto)
	-f	VDimage file
	-s	Snapshot file(s) to load on top of the image file
	-a	allow all users to read disk
	-w	allow all users to read and write to disk
	-g	run in foreground
	-v	verbose
	-d	debug

NOTE: you must add the line "user_allow_other" (without quotes)
to /etc/fuse.confand set proper permissions on /etc/fuse.conf
for this to work.

Mounting a VDF r/w:

./vdfuse -w -f box-disk1.vmdk /mnt/vdf_image
mount /mnt/vdf_image/Partition2 /mnt/part

If you also want to mount snapshots add them with -s to the command line

Known issues
============

There have been issues mounting VDFs writeable. Mounting them for writing seems not to be reliable
