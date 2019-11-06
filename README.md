# ps3

This repo is a collection of notes from my tests booting redribbon linux 3.12.6 from an NFS rootfs.

I use the petitboot binary wich is currently on github at https://github.com/nevik-xx/dtbImage.ps3

The ps3 need to be prepared before hand:

- from 3.41 or 3.55 install and do the necessary to install otheros++ 
- boot into otheros and run the live installer
- install it wherever you like (usb drive, usb key, internal HDD) I don't recommend internal since it will wipe your hard drive; instead install to a usb drive and reboot into it.
- upgrade your apt/source.list with method descripbe in tis article https://redribbongnulinux.000webhostapp.com/ ( basicaly you change all occurences “ftp.debian.org” to “archive.debian.org”.)
- sudo apt-get udpate&& sudo apt-get upgrade 
     If the upgrade completed without errors you can reboot to make sure you can actually boot into the new kernel 
     (actually i was able to install nfs-common and use the command sudo modprobe nfs to load the nfs module.
     

- once in the new kernel ( or before you reboot into it) 
     1) sudo apt-get install nfs-common && sudo modprobe nfs (or some debian variant of that)
     2) sudo update-initramfs -u  
     3) test and mount you nfs filesystem : sudo mkdir -p /mnt/nfs/ && sudo mount -o nolock IP_OF_NFS_SERVER:/nfs/ps3/rootfs /mnt/nfs/
     4) cd /mnt/nfs && sudo rsync -avz --exclude '/nfs/' /* /nfs/ (some variation of that) You can dd the usb drive from another linux installation (dd if=/dev/sdb1 of=/tmp/rootfs.img) and rsync the content to the nfs filesytem
     
     
     
 NETBOOTING: 
 
 TODO
     
     
