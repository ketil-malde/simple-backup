# A simple backup script

This is a simple script for incremental backups of my laptop.  It uses
LUKS to encrypt the backup device, and `rsync` to copy files.  The
copying uses hardlinks to the previous backup to avoid storing the
same file more than once.  The nice thing about this is that you get
incremental backups, but each backup is a browsable directory.

## Initializing the backup disk

(NB! I write this from memory. Caveat emptor.)

The backup volume needs to be initialized and configured with
LUKS.  Plug in your disk (I use an external USB drive), and run the
following commands

    sudo cryptsetup luksFormat /dev/sdX
	
Obviously replacing /dev/sdX with a valid path to the disk device.
This will destroy any contents on the device, you have been warned.
The command will ask you for your password and then a passphrase for
the encryption, make sure it's a good one. Then:

    sudo cryptsetup luksOpen /dev/sdX backup
	sudo mkfs.ext4 /dev/mapper/backup

This creates a file system on the encrypted volume.  Next, do:

    sudo cryptsetup luksClose backup
	
and your disk is ready for your first backup.

## Configuring the script

Before you run the script, edit it so that you initialize the
two variables `DISK` and `EXCLUDE` to suit your setup.  `DISK` should
refer to the disk you wish to backup to, do an `ls -l
/dev/dis/by-uuid` to check the alternatives.  `EXCLUDE` is a list of
paths that should be ignored for backup purposes.

## Taking backup

You should now be able to simply run the script (as root), it should unlock the
encrypted device (asking you for the passphrase), mount it, and make a
copy of your stuff to a directory named after the current date.  A
link called `current` will be created, pointing to the latest backup
(so incomplete backups won't be pointed to).

