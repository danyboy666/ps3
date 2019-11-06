# ps3

This repo is a collection of notes from my tests booting redribbon linux 3.12.6 from an NFS rootfs.

I use scattered information available around but most of the usefull info comes from 

http://ftp.riken.go.jp/pub/Linux/kernel/people/geoff/cell/ps3-howto/ps3-nfs-root-howto.txt

and 

http://jk.ozlabs.org/blog/post/158/netbooting-petitboot/


I use the petitboot binary wich is currently on github at https://github.com/nevik-xx/dtbImage.ps3

The ps3 need to be prepared before hand:

- from 3.41 or 3.55 install and do the necessary to install otheros++ 
- boot into otheros and run the live installer
- install it wherever you like (usb drive, usb key, internal HDD) I don't recommend internal since it will wipe your hard drive; instead install to a usb drive and reboot into it.
- upgrade your apt/sources.list with method described in this article https://redribbongnulinux.000webhostapp.com/ 

- sudo apt-get udpate && sudo apt-get upgrade 
     If the upgrade completed without errors you can reboot to make sure you can actually boot into the new kernel 
     (actually i was able to install nfs-common and use the command sudo modprobe nfs to load the nfs module. 

- once in the new kernel ( or before you reboot into it) 
     1) sudo apt-get install nfs-common && sudo modprobe nfs (or some debian variant of that)
     2) sudo update-initramfs -u  
     3) test and mount you nfs filesystem : sudo mkdir -p /mnt/nfs/ && sudo mount -o nolock IP_OF_NFS_SERVER:/nfs/ps3/rootfs /mnt/nfs/
     4) cd /mnt/nfs && sudo rsync -avz --exclude '/mnt/nfs' / /mnt/nfs (some variation of that) You can dd the usb drive from another linux installation (dd if=/dev/sdb1 of=/tmp/rootfs.img) and rsync the content to the nfs filesystem.
     
     
     
 NETBOOTING: 
 
 TODO
 
 pfSense : 
     
use a combination of DHCP options ; right now what's working for me (partially I still have to enter the initrd line manually)

( still need to figure out how to pass http as image and initrd string . i can only pass one line, even option 209 doesn not seem to work)
TFTP Server : IP_OF_TFTP_SERVER
Next server : same
root-path : IP_OF_NFS_SERVER/nfs/ps3/rootfs
option 67 string "http://IP_OF_TFTP_SERVER/pxelinux/kernel/ps3/vmlinux"
or
option 209 string "http://IP_OF_TFTP_SERVER/pxelinux/pxelinux.cfg/C0A879C8" # doesnt work as for now here it's content 

default ps3

label ps3
        kernel http://192.168.121.55/pxelinux/kernel/ps3/vmlinux
        initrd http://192.168.121.55/pxelinux/img/ps3/initrd.img
        append rootfstype=nfs nfsroot=192.168.121.40:/nfs/ps3/rootfs root=/dev/nfs ip=bootp fsck.repair=yes rw --




copy your new vmlinux and initrd.img from /rootfs/boot to pxe of tftp server (right now i can only use http weirdly tftp, nfs, everything else doesn't work, maybe because the main DHCP option foot filename applied to this LAN network is lpxelinux.0 even though i'm in the new DHCP pool with MAC restrictions is it possible i'm parsing the original protocol from the original lpxelinux.0 it get somehow passed as the absolute protocol even tought i'm not even using it?)

This needs to be sorted out but i can definitly reach the nfsroot when i pass both line manually

 image http://IP_OF_TFTP_SERVER/pxelinux/kernel/ps/vmlinux
    initrd http://IP_OF_TFTP_SERVER/pxelinux/img/ps/initrd.img
    append /dev/nfs rootfs=IP_OF_NFS_SERVER/nfs/ps3/rootfs
 
 Not necessary to pass other options.
 
 Fully booting onto red ribbon desktop 
 
 df-h partial output 
 
 IP_OF_NFS_SERVER:/nfs/ps3/rootfs    16G     15G  477M  97% /
 
 I edited fstab to prevent the original physical partition to be mounted as well as the swap line for now.
 Also network/interface

 This file describes the network interfaces available on your system
 and how to activate them. For more information, see interfaces(5).
 The loopback network interface
auto lo
iface lo inet loopback
 The wired network interface
allow-hotplug eth0
iface eth0 inet dhcp
manual eth0
iface eth0 inet dhcp
 The wireless network interface
auto wlan0



 /etc/fstab: static file system information.

 <file system>                                 <mount point>   <type>          <options>                               <dump>  <pass>
proc                                            /proc           proc            defaults                                0       0
##UUID=41603647-63ea-4484-8067-e6465d4fb2e0                                              /               ext4            relatime,defaults,errors=remount-ro                             0       1
##UUID=56b43361-0447-4fd5-b575-3592112b9da0                                              none            swap            sw                              0       0
/dev/cdrom                                      /media/cdrom0   udf,iso9660     user,noauto                             0       0


#You can add but it's not necessary
/dev/nfs    /    rootfs    defaults   0 0

(live)ps3@redribbon:~$ mount | grep root
192.168.121.40:/nfs/ps3/rootfs on / type nfs (rw,relatime,vers=3,rsize=524288,wsize=524288,namlen=255,hard,nolock,proto=tcp,port=2049,timeo=7,retrans=10,sec=sys,local_lock=all,addr=192.168.121.40)



(live)ps3@redribbon:~$ uname -a
Linux redribbon 3.12.6-red-ribbon-powerpc64-ps3 #7 SMP Tue Jan 7 17:09:59 CET 2014 ppc64 GNU/Linux


