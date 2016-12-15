# 安装基本系统
###基于BIOS主板，且在virtualbox里安装，整体参考[Installation guide](https://wiki.archlinux.org/index.php/Installation_guide)
* 检查主板，如果报文件不存在，那就不是UEFI主板

        ls /sys/firmware/efi/efivars
* 检查网络

        ping www.baidu.com
* 分区（GPT格式） 
    * 参考  
        * [Partitioning (简体中文)](https://wiki.archlinux.org/index.php/Partitioning_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E9.80.89.E6.8B.A9GPT_.E8.BF.98.E6.98.AF_MBR)
        * [GRUB (简体中文)](https://wiki.archlinux.org/index.php/GRUB_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
        * [fdisk](https://wiki.archlinux.org/index.php/Fdisk#gdisk)  
    * 操作

            gdisk /dev/sda  
        1. 具体操作看内部help
        2. 如果使用GRUB，必须第一个建立1个1M大小的类型为ef02的分区，这个分区不能格式化，当不存在  
        2. 第二个是类型8200是交换分区  
        2. 第三个是根分区，默认类型
        3. 最后输入w保存
    * 检查分区是否对齐
        
            parted /dev/sda
            (parted) align-check optimal 1
        提示1 aligned表示OK
* 格式化分区
        
        mkfs.ext4 /dev/sda2
        mkswap /dev/sda3
* 挂载
    
        mount /dev/sda2 /mnt #挂载主分区，如果还有home、var等分区，必须先挂载主分区，再依次挂载其他分区
        swapon /dev/sda3
* 选择镜像地址  
    参考[Mirrors](https://wiki.archlinux.org/index.php/Mirrors_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))  
    备注：清华那个最好选上，因为我安装的时候，有个软件，在163没有，在清华有
* 安装基本系统
    
        pacstrap /mnt base
* 配置
    * 生成分区表,二选一
        
            genfstab -U /mnt >> /mnt/etc/fstab #UUID
            genfstab -L /mnt >> /mnt/etc/fstab #labels
    * chroot
            
            arch-chroot /mnt
    * 时区
        
            ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
            hwclock --systohc #或hwclock --systohc --utc
    * 本地化
            
            vi /etc/locale.gen 增加
            en_US.UTF-8 UTF-8
            zh_CN.UTF-8 UTF-8

            echo LANG=en_US.UTF-8 > /etc/locale.conf    //暂时设置为英文
    * hostname
        
            vi /etc/hostname #填写自己想要的hostname
            vi /etc/hosts   #增加一条hostname对应的host
    * 网络配置
            
            ip link set eth0 up     #启用网卡eth0
            ip link set eth0 down   #禁用网卡eth0
        
            systemctl enable dhcpcd #暂时没有找到静态ip的文件设置，用dhcp
    * 如果修改了mkinitcpio.conf
        
            mkinitcpio -p linux
    * 引导器（GRUB）

            pacman -S intel-ucode #intel cpu需要安装
            pacman -S grub
            grub-install --target=i386-pc /dev/sda
            grub-mkconfig -o /boot/grub/grub.cfg
    * 退出并umount，重启
        
            exit
            umount -R /mnt
            shutdown -r now
