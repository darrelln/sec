---
layout: default
title: Time / Date
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## Fixing clock skew issues
*VirtualBox guests will sync time with the host unless otherwise disabled by VBoxManage, so check that first! See this link: https://www.winklerweb.net/index.php/blog/11-tools/25-disabling-time-synchronization-in-virtualbox-ubuntu-16-04.*

*Can't seem to get this to work on Kali, tested on Ubuntu 18.04.*

## The quick, easy fix if you're in a VM
Change the host's clock to the required time so it syncs to the guest.

## The more permanent fix if you're in a VM
### On the host
Tested on Ubuntu 18 (this caused issues on Kali). To disable host time sync in VirtualBox, on the host, update the VM to disable client time sync:
```csharp
// List the VMs to get the name.
VBoxManage list vms

// Disable time sync.
VBoxManage setextradata "Ubuntu 18 HTB" "VBoxInternal/Devices/VMDev/0/Config/GetHostTimeDisabled" 1

// To revert the change.
VBoxManage setextradata "Ubuntu 18 HTB" "VBoxInternal/Devices/VMDev/0/Config/GetHostTimeDisabled"
```

*THIS CHANGE PREVENTED THE VM FROM STARTING. REMOVING THE CHANGE AND GOING WITH ONLY THE CHANGES OUTLINED BELOW ON THE GUEST PREVENTED TIME SYNC FROM VIRTUALBOX TO THE GUEST, WHICH SOLVED THE ISSUE.*

### On the guest
Edit `/opt/VBoxGuestAdditions-5.1.2/init/vboxadd-service` and in `start()` replace;
```
daemon $binary --pidfile $PIDFILE > /dev/null
```

with:
```
daemon $binary --disable-timesync --pidfile $PIDFILE > /dev/null
```

In the same file, look for the definition of `daemon()` and add the fourth argument:
```csharp
$1 $2 $3 $4
```

Finally, add the fourth argument to the daemon() method of the start-stop-daemon method:
```
if which start-stop-daemon >/dev/null 2>&1; then
  daemon() {
    start-stop-daemon --start --exec $1 -- $2 $3 $4
  }
```

That's all the changes required in the vboxadd-service service. Restart the service:
```
// Reload the daemon units.
systemctl daemon-reload

// Restart the service.
systemctl restart vboxadd-service.service

// Check service status.
systemctl status vboxadd-service.service

// More details if the service fails to start.
journalctl -xe
```

### Disable NTP time sync on Ubuntu
Disable automatic updates by setting the system clock to manual updates, otherwise the time will keep changing automatically:
```csharp
// Stop automatic time updates.
timedatectl set-ntp 0

// Check status.
timedatectl status
```

Finally, sync the time with the remote system:
```
// Sync with remote server.
sudo date --set "`rdate -vpn egotistical-bank.local | awk 'NR==1'`"
```