# ansible-arch-linux

A set of `ansible` playbooks for provisioning Arch Linux.

---
**NOTE**

Work in Progress,
trying ansible to install archlinux.

If you want to use this as template,

- modify user data in `group_vars/all.yml`.
- possibly add a machine beyond `host_vars/vbox.yml`

---

## How to run

### Create bootable media

1. Download and verify latest ArchLinux ISO:

   ```sh
   make iso
   ```

2. Write the ISO image to a USB flash drive:

   ```sh
   dd bs=4M if=archlinux-$VERSION-dual.iso of=/dev/sdX status=progress && sync
    ```

### Install OS

1. Boot into ArchLinux Live CD and connect to the Internet.

   If via Wifi, start `iwctl` prompt:

   ```sh
   station wlan0 connect <ssid>
   ```

2. Then

   ```sh
   mount -o remount,size=1G /run/archiso/cowspace
   pacman -Sy git ansible
   git clone https://github.com/rpuntaie/ansible-arch-linux aal
   cd aal
   ansible-playbook install.yml
   ```

3. After the reboot, login into the new system (as specified in `group_vars/all.yml`,
   possibly configure Wifi via `nmtui`,
   and run `ansible` to install and configure the full-featured Arch Linux:

   ```sh
   cd aal
   ansible-galaxy install kewlfft.aur
   ansible-playbook --ask-become-pass configure.yml
   ```

---
**NOTE**

Intead of from github you can nfs-mount `ansible-arch-linux`:

```sh
# on server's /etc/exports::
#   /home/roland/mine/ansible-arch-linux *(rw,sync,fsid=0)
#   #sudo exportfs -arv
mkdir aal
mount -t nfs 192.168.1.108:/home/roland/mine/ansible-arch-linux aal
cd aal

```
---

## Post-setup

### Add fingerprint authentication

```sh
sudo fprint-enroll roland
```

### Notes

Generating SSH key:

```sh
ssh-keygen -t ed25519 -C "${USER}@${HOSTNAME}-$(date -I)"
```
