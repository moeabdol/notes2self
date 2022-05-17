# What is LUKS?

The Linux Unified Key Setup (LUKS) is a disk encryption specification created by
Clemens Fruhwirth in 2004 and was originally intended for Linux.

While most disk encryption software implements different, incompatible, and
undocumented formats, LUKS implements a platform-independent standard on-disk
format for use in various tools. This not only facilitates compatibility and
interoperability among different programs, but also assures that they all
implement password management in a secure and documented manner.

[Wikipedia][1]

# How to Create a LUKS Encrypted USB Stick

1. Insert usb stick and run `$ sudo fdisk -l` to list all drives.
2. Identify your usb drive for example `/dev/sdb`.
3. Run `gdisk` or `cfdisk` to format your drive and create new partitions.
4. Once formatted you can run the following to create an encrypted drive.
   ```
   $ sudo cryptsetup --cipher aes-xts-plain64 --hash sha512 --use-random
   --verify-passphrase /dev/sdb1
   ```
5. Note that the above is customized cipher which I picked. You can run the
   command using the default cipher mentioned in the `--help`.
6. The command will prompt you for the encryption password. Once that is entered
   then your drive will be encrypted.
7. You can now open your drive. Note that you have to give it a mapper name in
   this case `enc_usb` which is a virtual drive that will be mapped under
   `/dev/mapper/enc_usb`.
   ```
   $ sudo cryptsetup luksOpen /dev/sdb1 enc_usb
   ```
8. Create the file system.
   ```
   $ sudo mkfs.ext4 /dev/mapper/enc_usb
   ```
9. Mount your drive.
   ```
   $ sudo mount /dev/mapper/enc_usb /media/usb
   ```
10. Go ahead and cp files into your newly created drive, and once done you can
    unmount and lock your drive.
    ```
    $ sudo umount /media/usb
    $ sudo cryptsetup luksClose enc_usb
    ```

# To Remove LUKS Encryption from Your Drive

1. Run the following. This will remove LUKS and return the drive to an
   unencrypted state.
   ```
   $ sudo cryptsetup luksRemoveKey /dev/sdb1
   ```

**Note:** Now everytime you need to use your encrypted drive you have to unlock
it, mount it, then once done lock it.


[1]: https://en.wikipedia.org/wiki/Linux_Unified_Key_Setup



