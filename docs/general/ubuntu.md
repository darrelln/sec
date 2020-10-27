---
layout: default
title: Ubuntu
parent: GENERAL
has_children: false
has_toc: true
---

{% include toc.html %}

## Ubuntu failing to boot and dropping to initramfs
Ubuntu failed to boot, dropped to BusyBox internal initramfs prompt:
```
// To check for the UNEXPECTED INCONSISTENCY message
(initramfs) exit

// Run the file system check
(initramfs) fsck /dev/mapper/ubuntu--vg-root -y

// Reboot - if this doesn't do anything the CTRL+ALT+DELETE
(initramfs) reboot
```

This is what it looks like in full:
```
BusyBox v1.18.5 (Ubuntu 1:1.18.5-1ubuntu4) built-in shell (ash) 
Enter 'help' for a list of built-in commands.

(initramfs) exit

/dev/mapper/ubuntu--vg-root: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY.
(i.e., without -a or -p options) 
fsck exited with status code 4. 
The root filesystem on /dev/mapper/ubuntu--vg-root requires a manual fsck. 

BusyBox v1.18.5 (Ubuntu 1:1.18.5-1ubuntu4) built-in shell (ash)
Enter 'help' for a list of built-in commands.

(initramfs) fsck /dev/mapper/ubuntu--vg-root -y

fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
/dev/mapper/ubuntu--vg-root contains a file system with errors, check forced.
```

See https://askubuntu.com/questions/137655/boot-drops-to-a-initramfs-prompts-busybox

## No IPv4 Being Assigned
Ubuntu server on DHCP not picking up IPV4 address.

Try releasing the DHCP lease and requesting a new one:
```
// Release lease.
sudo dhclient -r enp0s3

// Renew lease.
sudo dhclient enp0s3
```

## Set static IP
Use netplan for this. Go to /etc/netplan/ and backup the .yaml configuration file. It'll likely be either 01-netcfg.yaml or 50-cloud-init.yaml. Now update the configuration as below:
```
// Current DHCP configuration looks (something) like this:
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: yes

// To configure a static IP, change to:
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
     dhcp4: no
     addresses: [172.16.6.10/24]
     gateway4: 172.16.6.1
     nameservers:
       addresses: [172.16.6.1]
```

## Kali
Black screen after running updates on Kali
This applies to Kali VM guests on VirtualBox.

**DO NOT UPDATE KALI!** Wait for the latest version and move to that. However, if you do have an issue, import a new Kali instance onto the same network segment and use wget to move files over. After logging into Kali and getting the black screen, open the soft keyboard for the VM and click Ctrl + Alt + F1 to open a terminal. You can then use the terminal as normal to move directories over to the new instance. 