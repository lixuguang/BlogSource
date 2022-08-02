---
title: OracleLinux内核从uek切换到uek
date: 2022-08-02 18:00:00
categories: 
  - OracleLinux
tags: 
  - kernel
---

```ssh
#/boot/grub/grub.conf 缺失：
 
yum install -y grub
grub-mkconfig -o /boot/grub/grub.conf
 
#/boot/grub2/grub.cfg 缺失：
 
yum install -y grub2
grub2-mkconfig -o /boot/grub2/grub.cfg

uname -a
sudo grep ^menuentry /boot/grub2/grub.cfg | awk -F\' '{print $2}' | nl -v0

sudo grub2-editenv list

sudo grep GRUB_DEFAULT /etc/default/grub

sudo vim /etc/default/grub

sudo grub2-set-default 1

sudo grub2-mkconfig -o /boot/grub2/grub.cfg

sudo dracut --force

sudo reboot

uname -a
```