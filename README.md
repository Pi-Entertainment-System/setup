# PES Setup

Repository containing all the files and scripts required to set-up PES.

[Ansible](https://www.ansible.com/) is used to automate the installation of PES 3.0 for development and deployment.

PES uses [Arch Linux Arm](https://archlinuxarm.org/) and is available on the Raspberry Pi.

Pre-built images and user documentation can be found for PES 2.X at: https://pes.mundayweb.com

**Note:** PES 3.0 is under development

## Installation of Arch Linux

In all cases at least a 8GB SD card is required.

Create a 50MB fat32 partition followed by an ext4 partition of at at least 6GB.

Insert the SD card into your Linux system and proceed as follows, substituting the correct device as necessary.

### Raspberry Pi 2/3

```
mkdir root boot
wget http://os.archlinuxarm.org/os/ArchLinuxARM-rpi-2-latest.tar.gz
mount /dev/sdf1 boot
mount /dev/sdf2 root
bsdtar -xpf ArchLinuxARM-rpi-2-latest.tar.gz -C root
sync
mv root/boot/* boot/
```

### Raspberry Pi 4

```
mkdir root boot
wget http://os.archlinuxarm.org/os/ArchLinuxARM-rpi-4-latest.tar.gz
mount /dev/sdf1 boot
mount /dev/sdf2 root
sync
mv root/boot/* boot/
```

### SSH modifcations

Whilst still mounted, edit `root/etc/ssh/sshd_config` and add:

```
PermitRootLogin yes
```

Now add your SSH key(s) to the root account in the image:

If you don't have any SSH keys set-up on your Linux host, make them now else skip this command:

```
ssh-keygen -t ed25519
```

Create `authorized_keys` file on Raspberry Pi file system:

```
cd root/root
mkdir .ssh
chmod 0700 .ssh
cp ~/.ssh/id_ed25519.pub .ssh/authorized_keys
chmod 0600 .ssh/authorized_keys
cd ../..
```

Finally:

```
umount boot
umount root
```

Now you can boot your Raspberry Pi.

## First Boot

Once your Raspberry Pi has booted, `ssh` in as root and run the following commands:

```
pacman-key --init
pacman-key --populate archlinuxarm
pacman -Syu
reboot
```

## Check out this Repository

```
pacman -S ansible git
git clone https://github.com/Pi-Entertainment-System/pes-setup
cd pes-setup
```

At this point you can enable WiFi if needed e.g.:

```
ansible-playbook -e 'wifi_psk=WIFI_PASSWORD wifi_ssid=WIFI_NETWORK' ansible/enable-wifi.yml
```

Set *WIFI_PASSWORD* and *WIFI_NETWORK* as necessary.

Now set-up Arch Linux for PES:

```
ansible-playbook ansible/setup-playbook.yml
```
