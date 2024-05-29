# Getting Started

## Install Kali VM on UTM:

[https://www.kali.org/docs/virtualization/install-utm-guest-vm/](https://www.kali.org/docs/virtualization/install-utm-guest-vm/)

Enable shared clipboard and share local time.

## Update Base OS

```
sudo apt update
sudo apt upgrade
```

Fix missing or autoremove if necessary:

```
sudo apt upgrade --fix-missing
sudo apt autoremove
```

## Install SPICE tools

```
sudo apt install spice-vdagent
sudo systemctl start spice-vdagent
sudo systemctl enable spice-vdagent
reboot
```

## Mount Shared Drive

```
sudo mkdir /mnt/share
sudo chown -R kali /mnt/share
sudo mount -t 9p -o trans=virtio share /mnt/share
```

## Add OffSec VPN

```
cp /mnt/share/universal.ovpn .
```

This is where the base configurations end for the extras template clone. 

## Running x86 Code on ARM Kali

Initial install:

```
sudo apt install -y qemu-user-static binfmt-support 
sudo dpkg --add-architecture amd64 
sudo apt update
sudo apt install libc6:amd64
```

If not automatically ran with qemu, run this:

```
qemu-x86_x64-static my_x86_code
```

There is also a base configuration here. 
# Non-Default Packages

```
sudo apt install powershell
```

AWS CLI:

```
sudo apt install awscli
```

Cloudfox:

```
wget https://github.com/BishopFox/cloudfox/releases/download/v1.14.0/cloudfox-linux-amd64.zip
unzip cloudfox-linux-amd64.zip
export PATH=$PATH:~/cloudfox
```

```
pip install principalmapper
export PATH=$PATH:/home/kali/.local/bin
```

Terraform:

```
wget https://releases.hashicorp.com/terraform/1.8.4/terraform_1.8.4_linux_arm64.zip
unzip terraform_1.8.4_linux_arm64.zip
```