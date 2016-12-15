# 配置图形界面

* 初始工作
    
        systemctl enable dhcpcd
        systemctl enable sshd
        #远程ssh登陆的话，编辑/etc/ssh/sshd_config,增加
        PermitRootLogin yes
        echo LANG=zh_CN.UTF-8 > /etc/locale.conf
* 软件

        pacman -S vim sudo awesome xorg-xinit numlockx xorg-server aria2
        pacman -S ttf-inconsolata ttf-dejavu wqy-bitmapfont wqy-microhei wqy-zenhei
* 安装图形驱动  
    如果是在virtualbox里安装archlinux，需要这个图形驱动，参考[在 VirtualBox 中安装 Archlinux](https://wiki.archlinux.org/index.php/VirtualBox_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#.E5.9C.A8_VirtualBox_.E4.B8.AD.E5.AE.89.E8.A3.85_Archlinux)，其他显卡驱动请参考archlinux官网
        
        pacman -S virtualbox-guest-utils
* 创建普通用户
	
		useradd -d /home/saxon saxon
		mkdir /home/saxon;chown saxon:saxon /home/saxon;chmod 700 /home/saxon

* 配置普通用户的图形
        
        su - saxon
        cp /etc/X11/xinit/xinitrc ~/.xinitrc
        vim .xinitrc 删除最后几行，添加
        numlockx &
        exec awesome
* 配置终端登陆自动启动startx
        
        cp /etc/skel/.bash_profile .
        vim ~/.bash_profile 添加
        if [ -z "$DISPLAY" ] && [ -n "$XDG_VTNR" ] && [ "$XDG_VTNR" -eq 1 ]; then
            exec startx
        fi

* 重启后用saxon登陆，会自动启动awesome

