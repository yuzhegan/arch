# archlinux setup script

关于arch安装的一个脚本

### Download
无**Uefi* *的可以用下面的脚本
```
dhcpcd
wget linuxyu.oss-cn-beijing.aliyuncs.com/setup.sh
```
电脑有**Uefi** 的可以用一下脚本
```
dhcpcd
wget linuxyu.oss-cn-beijing.aliyuncs.com/setup-efi.sh
```


无法链接github的朋友可以使用下面的链接
```
wget gitee.com/xylzq/arch/raw/master/setup.sh
```
### Usage
- 执行 bash setup.sh
- 脚本运行前可根据需要更改分区位置大小格式及启动项等.
- 脚本运行中需要确定是否更新，填写域名，用户名及密码等.
- 脚本运行结束后重启即可基本使用.
- 桌面环境可重启后根据需要自行安装
- 内核安装的是linux-lts，可根据需求自行更改，在安装基本系统处

### 几个桌面环境安装方法
- KDE：
```
pacman -S kf5 kf5-aids plasma kdebase kdegraphics kde-l10n-zh_cn sddm
systemctl enable sddm
```
- Gnome：
```
pacman -S gnome gnome-terminal gnome-tweak-tool chrome-gnome-shell gdm
systemctl enable gdm
```
- Xfce：
```
pacman -S xfce4 xfce4-goodies sddm
systemctl enable sddm 
```
- Deepin：
```
pacman -S deepin deepin-extra deepin-terminal lightdm lightdm-gtk-greeter
systemctl enable lightdm
nano /etc/lightdm/lightdm.conf
# 将greeter-session=example-gtk-gnome改为greeter-session=lightdm-deepin-greeter
```

# archlinux config script

关于arch配置美化的一个脚本，其中**config** 脚本中包含了*Kde* 的桌面环境

### 一些注意事项
当安装完桌面可能进入不了桌面，需要安装下
```
sudo pacman -S xorg xorg-service
```

授权的用户可能进入不了桌面，可以设创建置下用户权限，或者重新一个用户进入系统
```
sudo useradd -m -G wheel david
```



### Download
```
sudo pacman -S wget git
wget linuxyu.oss-cn-beijing.aliyuncs.com/config.sh
bash config.sh
```

### Fcitx5 问题
装完输入法可能在终端或某些程序中无法使用的情况
将以下代码加入到~/.xprofile

```
export LANG=zh_CN.UTF-8
export LANGUAGE=zh_CN:en_US
export GTK_IM_MODULE=fcitx5
export QT_IM_MODULE=fcitx5
export XMODIFIERS="@im=fcitx5"

```




如果还是不行就做以下操作：
将以下代码加入到 `～/.xprofile` 
```
export LC_ALL=zh_CN.UTF-8
export XIM=fcitx5
export XIM_PROGRAM=fcitx5
export GTK_IM_MODULE=fcitx5
export QT_IM_MODULE=fcitx5
export XMODIFIERS="@im=fcitx5"
exec fcitx5&
```
然后在编辑下`vim ~/.config/fcitx5/profile` 
```
[Groups/0]
# Group Name
Name=Default
# Layout
Default Layout=us
# Default Input Method
DefaultIM=pinyin

[Groups/0/Items/0]
# Name
Name=keyboard-us
# Layout
Layout=

[Groups/0/Items/1]
# Name
Name=pinyin
# Layout
Layout=

[GroupOrder]
0=Default
```

再修改下`~/.pam_environment `, 加入以下内容：
```
GTK_IM_MODULE=fcitx5
QT_IM_MODULE=fcitx5
XMODIFIERS="@im=fcitx5"
```
Thinkpad x1c trackpoint
```bash
sudo pacman -S xorg-xinput
#显示所有输入设备，找到TrackPoint的id
xinput list
#我的笔记本上TrackPoint的id是12                    

vi ~/scripts/trackpoint.sh # add blow content


xinput set-int-prop 12 "Evdev Wheel Emulation" 8 1
xinput set-int-prop 12 "Evdev Wheel Emulation Button" 8 2
xinput set-int-prop 12 "Evdev Wheel Emulation Timeout" 8 200
xinput set-int-prop 12 "Evdev Wheel Emulation Axes" 8 6 7 4 5
# 然后…高速~~高灵敏度~~~
sudo nvim /etc/rc.local

echo -n 120 > /sys/devices/platform/i8042/serio1/speed
echo -n 250 > /sys/devices/platform/i8042/serio1/sensitivity

chmod +x /etc/rc.local

then reboot the mechine

```
硬件改caps_lock2esc
```
git clone https://gitlab.com/interception/linux/plugins/caps2esc.git
cd caps2esc
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build

```
1. 大佬直接提供了AUR包，arch/Manjaro直接使用下列代码安装
```fish
yay -S interception-tools
sudo ln -s caps2esc/build/caps2esc /usr/bin/caps2esc
# 将编译好的
```

2. 安装好了以后，在`安装好了以后，在/etc/udevmon.yaml添加下列代码` 添加下列代码
```
- JOB: intercept -g $DEVNODE | caps2esc | uinput -d $DEVNODE
  DEVICE:
    EVENTS:
      EV_KEY: [KEY_CAPSLOCK, KEY_ESC]
```
3. 在`在/etc/systemd/system/udevmon.service中添加下列代码` 中添加下列代码
```config
[Unit]
Description=udevmon
Wants=systemd-udev-settle.service
After=systemd-udev-settle.service

[Service]
ExecStart=/usr/bin/nice -n -20 /usr/bin/udevmon -c /etc/udevmon.yaml

[Install]
WantedBy=multi-user.target

```

4. 最后使用`最后使用sudo systemctl enable --now udevmon来启动` 来启动.




### 无法调节屏幕亮度的问题
调节屏幕亮度的文件有执行权限`light -A -U` 才有用
```
sudo chmod a+rw /sys/class/backlight/intel_backlight/brightness

```

### Qv2ray代理的安装
```
aria2c https://linuxyu.oss-cn-beijing.aliyuncs.com/qv2ray.zip
```


给文件添加可执行权限
```
sudo chmod a+x Qv2ray-v2.7.0-linux-x64.AppImage

```
### 安装`kite` 

```
aria2c https://centos5.oss-us-east-1.aliyuncs.com/kite.zip
```
将解压出来的`kite/kitexx`  将kitexx改名为kite
再将`kite/kite`  这个文件夹放入到`.local/share` 目录下

### 安装Dwm
安装完成之后将`dwm` 加入到系统启动项里面
新建`/usr/share/xsessions/dwm.desktop` 中间某个目录没有就创建他
在`dwm.desktop` 加入一下内容
```
[Desktop Entry]
Encoding=UTF-8
Name=Dwm
Comment=Dynamic window manager
Exec=dwm
Icon=dwm
Type=XSession

```
### U盘在linux中拷贝文件的方式


#### 备份
下面就是`pigz` 备份archliunx系统, 执行命令的路径为`/` 
```
sudo tar --use-compress-program=pigz -cvpf arch-backup.tgz --exclude=/proc --exclude=/lost+found --exclude=/arch-backup.tgz --exclude=/mnt --exclude=/sys --exclude=/run/media  --exclude=/media  /

```
`--exclude`后面跟的是需要排除的路径，根据自己文件系统挂载情况觉得

`--exclude=/arch-backup.tgz` 这个一定要写，要不会进入四循环

linux插入U盘，需要用到硬盘工具`fdisk` 

`fdisk -l` 查看所有的硬盘，如果你的U盘是系统引导盘，需要将闲置空间分为一个扇区方法是
```
fdisk /dev/sde
```

先进入U盘，进入之后按`n` 新建分区，两个`Enter` ,将所有闲置空间留给`sde3`拷贝的方式是将U盘挂载到系统的`/mnt`
```
mount /dev/sde3 /mnt
cd /mnt
ls
sudo cp /arch-backup.tgz /mnt
```

#### 还原

[archlinux迁移](https://blog.csdn.net/taotieren/article/details/111413045) 其中需要注意的是`liveCD` 中有三个分区，分别是`sde1` `sde2` `sde3` , 前两个分区是系统的引导，`sde3` 中包含系统的备份`arch-backup.tgz` , 我们需要将`sde3` 挂载到`/mnt/arch/backup` 文件夹中，没有就新建
``` 
sudo mount /dev/sde3 /mnt/arch/backup
cd /mnt/arch/backup

```

下面就是`pigz` 恢复archliunx系统
```
sudo tar --use-compress-program=pigz -cvpf arch-backup.tgz -C /mnt/arch

```
#### Macbook 觸控板手势
```
yay -S xf86-input-mtrack
```

`sudo nvim /etc/X11/xorg.conf.d/50-mtrack.conf` 加入下面内容:
```
Section "InputClass"
    MatchIsTouchpad "on"
    Identifier      "Touchpads"
    Driver          "mtrack"

    Option "TapDragTime" "150"
    Option "Sensitivity" "0.35"
    Option "AccelerationProfile" "2"
    Option "ConstantDeceleration" "2.5"
    Option "AccelerationVelocityScaling" "5"

    Option "ScrollDistance" "150"
    Option "ScrollUpButton" "5"
    Option "ScrollDownButton" "4"
    Option "ScrollLeftButton" "7"
    Option "ScrollRightButton" "6"
    Option "ScrollCoastDuration" "500"
    Option "ScrollCoastEnableSpeed" "5"

    Option "SwipeClickTime" "0"
    Option "SwipeSensitivity" "1200"

    Option "SwipeDistance" "1"
    Option "SwipeLeftButton" "1"
    Option "SwipeRightButton" "1"
    Option "SwipeUpButton" "1"
    Option "SwipeDownButtn" "1"

    Option "Swipe4LeftButton" "9"
    Option "Swipe4RightButton" "8"
    Option "Swipe4UpButton" "11"
    Option "Swipe4DownButton" "10"

    Option "ScaleDistance" "300"
    Option "ScaleUpButton" "12"
    Option "ScaleDownButton" "13"
    Option "RotateLeftButton" "0"
    Option "RotateRightButton" "0"
EndSection
o
```

`sudo vim /etc/X11/xorg.conf.d/10-mtrack.conf` 加入下面内容：
```
Section "InputClass"
  Identifier "Multitouch Touchpad"
  Driver "mtrack"
  MatchDevicePath "/dev/input/event*"
  MatchProduct "bcm5974"
  MatchIsTouchpad "true"
EndSection
```

代码实现了`自然滚动` `滚动惯性` `三指拖放` `光标加速` `四指滑动` , 三指可以拖放选择，四指可以实现前进后退

### macbook电脑`fn` `left_ctrl` 互换
```
yay -S hid-apple-patched-git-dkms 
```

然后在编辑`hid-apple-patched-git-dkms ` 配置文件之后重启就能生效,`/etc/modprobe.d/hid_apple.conf` 
```
options hid_apple swap_fn_leftctrl=1
```
至此对`macbook` 的特殊配置基本完成
还有一个风扇的驱动`mbpfan` :
```
yay -S mbpfan
```






### explain
- 主要配置有，添加archlinuxcn等源
- 桌面环境汉化及中文输入法
- 一些基本主题美化，如zsh，图标主题包等
- 一些必要软件如压缩，挂载，声音管理器
- 一些实用软件如文档管理器，播放器，网易云音乐，wps,火狐浏览器等
- 大家可根据需要自由增减
- 安装完zsh后脚本会自动退出，所以zsh的配置脚本无法运行，大家可以手动操作脚本的内容


# 安装完archlinux后要做的几件事

1.更换内核(linux比较激进，不够稳定)
```
sudo pacman -S linux-lts
sudo grub-mkconfig -o /boot/grub/grub.cfg 
# 安装内核头文件（某些软件需要，如virtualbox）
# sudo pacman -S linux-lts-headers 
sudo pacman -Rs linux
```

2.安装微代码microcode（可修正cpu硬件错误）
```
# intel
sudo pacman -S intel-ucode
# amd
sudo pacman -S amd-ucode
sudo grub-mkconfig -o /boot/grub/grub.cfg 
```

3.提升显卡性能
```
一、CUDA
sudo pacman -S cuda
二、opencl
# intel和amd
sudo pacman -S opencl-headers opencl-mesa lib32-opencl-mesa
# nvidia
opencl-nvidia lib32-opencl-nvidia
```
4.添加window字体
```
sudo mkdir /usr/share/fonts/WindowsFonts
将windows字体全部复制到这个文件夹，一般在C：/windows/Fonts 里面
cd /usr/share/fonts/WindowsFonts
sudo fc-cache -fv
# archlinux则直接：
yaourt -S ttf-ms-fonts
```

5.安装防火墙
```
sudo pacman -S ufw
sudo ufw enable
sudo ufw status verbose
sudo systemctl enable ufw.service
```

6.加密家目录
```
# 使用eCryptFS
# 重启进入tty2
# root进入查看$home进程（如果有程序为活跃状态，则杀死它）
ps -U $home
# 安装必要安装包
pacman -S rsync lsof ecryptfs-utils
modprobe ecryptfs
ecryptfs-migrate-home -u $username
exit
ls
cat README.txt
ecryptfs-mount-private
ecryptfs-unwrap-passphrase
# 然后配置
```

7.删除孤立的包
```
sudo pacman -Rns $(pacman -Qtdq)
```

8.提高数据库访问速度(固态硬盘不可使用)
```
sudo pacman-optimize && sync
```
9.检查系统文件错误
```
sudo systemctl --failed
sudo journalctl -p 3 -xb
```
10.多系统管理

- 生成多系统引导程序
```
# 探寻其他系统
sudo pacman -S os-prober
# 生成配置文件
sudo grub-mkconfig -o /boot/grub/grub.cf
```
- 多系统时间同步
```
# 安装 Windows 和 Linux 系统后 Windows 的时间会比慢8个小时
————————————————
# 原因：

# 电脑系统中有两个时间：
# 硬件时间：保存在主板中，信息比较少没时区、夏令时的概念
# 系统时间：又系统维护，独立于硬件时间，拥有时区、夏令时等信息
# 系统时间又因为系统的不同使用了两种时间管理办法：
# localtime：本地时间，目前只有 Windows 在使用。
# UTC：是一种世界标准时间，Linux 这类类 UNIX 多数会使用，UTC 加减时区之后才是本地时间。
# Windows 认为硬件时间就是本地时间，所以会直接把主板中的时间拿来当做当前的时间。设置或同步时间后也会把“正确”的时间写入主板
# Linux 认为硬件时间是 UTC 标准时间，Linux 时间同步后会把“正确”的时间 -8 之后作为标准 UTC 标准时间写入主板
————————————————
# 解决办法：

# windows:
# 以管理员身份使用运行
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f
# 以上方法无效或64位系统：
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_QWORD /f
————————————————
# linux：
sudo timedatectl set-local-rtc true
```
- 将其他系统(分区)自动挂载到家目录
```
# 在家目录创建挂载点（挂载点最好为空文件夹，非空文件夹文件在挂载后将不会显示，除非卸载此挂载点）
mkdir win7
# 查看挂载分区uuid
sudo blkid
# 查看挂载分区类型
sudo fdisk -l
# 将挂载信息填入fstab文件（除了结尾的00之间使用空格，其他全部用跳格tab分割）

sudo nano /etc/fstab
----------------------------------------------------------------------
# /dev/sdb3
UUID=xxxxxxxx   /home/$username/win7  ntfs    defaults        0 0

```
**11.备份！！！**

# archlinux application

关于arch的一些实用软件

1.录屏软件
```
sudo pacman -S simplescreenrecorder
```
2.显示按键软件
```
sudo pacman -S screenkey
```
3.剪辑视频软件
```
sudo pacman -S kdenlive
```
4.修图软件
```
sudo pacman -S gimp
```
5.vmware
```
# 安装必要依赖
sudo pacman -S fuse2 gtkmm linux-headers pcsclite libcanberra
yay -S --noconfirm --needed ncurses5-compat-libs

# 安装虚拟机
yay -S --noconfirm --needed  vmware-workstation

# 根据需要，启用以下某些服务：
# 1]、用于访客网络访问的vmware-networks.service
# 2]、vmware-usbarbitrator.service用于将USB设备连接到guest虚拟机
# 3]、vmware-hostd.service用于共享虚拟机
sudo systemctl enable vmware-networks.service  vmware-usbarbitrator.service vmware-hostd.service
sudo systemctl start vmware-networks.service  vmware-usbarbitrator.service vmware-hostd.service
# 确认服务状态：
sudo systemctl status vmware-networks.service  vmware-usbarbitrator.service vmware-hostd.service
# 加载VMware模块：
sudo modprobe -a vmw_vmci vmmon

# 启动虚拟机，填写密钥
sudo vmware
```

6.virtualbox
```
# 安装virtualbox虚拟机
1]、安装软件包
sudo pacman -S virtualbox
2]、安装内核模块
sudo pacman -S virtualbox-host-dkms
3]、加载模块
modprobe vboxdrv (vboxnetadp vboxnetflt vboxpci)
4]、从客体系统访问主机 USB 设备
    - 将需要运行 VirtualBox 的用户名添加到 vboxusers 用户组
sudo usermod -G vboxusers -a $USERS(如primary)

# 开启VT-x/AMD-V功能
1]、查看系统列表
VBoxManage list vms
2]、开启指定系统次功能
VBoxManage modifyvm "$SYSTEMS(如：win7)" --nested-hw-virt on

# virtualbox内的linux系统无法打开共享文件夹解决办法
1]、方法一：将虚拟机中，使用者加入虚拟用户组，重启
sudo usermod -a -G vboxsf $USERS(如primary)
2]、方法二：修改虚拟机中，共享文件夹权限
sudo chmod -R 777 /media

# 安装MacOS系统
1]、查看硬件信息
xrandr & sysinfo
2]、创建虚拟机 MacOSx
 # 寻找网上资源（.vmdk文件）并按文件配置创建该系统
 - 主机命令行调整该系统参数
 # VBoxManage modifyvm MacOSx  --cpuidset 00000001 000306a9 00020800 80000201 178bfbff
   VBoxManage setextradata "MacOSx" "VBoxInternal/Devices/efi/0/Config/DmiSystemProduct" "iMac11,3"
   VBoxManage setextradata "MacOSx" "VBoxInternal/Devices/efi/0/Config/DmiSystemVersion" "1.0"
   VBoxManage setextradata "MacOSx" "VBoxInternal/Devices/efi/0/Config/DmiBoardProduct" "Iloveapple"
   VBoxManage setextradata "MacOSx" "VBoxInternal/Devices/smc/0/Config/DeviceKey" "ourhardworkbythesewordsguardedpleasedontsteal(c)AppleComputerInc"
   VBoxManage setextradata "MacOSx" "VBoxInternal/Devices/smc/0/Config/GetKeyFromRealSMC" 1
3]、安装系统

```

6.邮件
```
sudo pacman -S thunderbird
```
7.下载器
```
sudo pacman -S transmission-qt 或者 transmission-gtk
sudo pacman -S qbittorrent
```
8.打印机
```
# 安装通用打印驱动系统cups
sudo pacman -S cups
# 启动cups并加入自启动
sudo systemctl start cups
sudo systemctl enable cups
# 下载开源驱动程序包（库）
sudo pacman gutenprint

# 其他驱动程序包
- 佳能
sudo pacman -S cndrvcups-lb
- 惠普
sudo pacman -S hplip
```
9.游戏管理器lutris
```
# 安装必备驱动：
# 进入官网https://github.com/lutris/docs/blob/master/InstallingDrivers.md按照提示根据系统下载相应驱动：
sudo pacman -S nvidia-dkms nvidia-utils lib32-nvidia-utils nvidia-settings vulkan-icd-loader lib32-vulkan-icd-loader
（lts内核再安装 pacman -S nvidia-lts ）

# 安装安装litris和它相关的包：
sudo pacman -S dxvk wine-staging lutris

# 安装wine-staging的可选依赖（为了完整运行window程序，最好都安装好）：
# 进入官网https://github.com/lutris/docs/blob/master/WineDependencies.md按照提示根据系统下载相应依赖：
sudo pacman -S wine-staging giflib lib32-giflib libpng lib32-libpng libldap lib32-libldap gnutls lib32-gnutls mpg123 lib32-mpg123 openal lib32-openal v4l-utils lib32-v4l-utils libpulse lib32-libpulse libgpg-error lib32-libgpg-error alsa-plugins lib32-alsa-plugins alsa-lib lib32-alsa-lib libjpeg-turbo lib32-libjpeg-turbo sqlite lib32-sqlite libxcomposite lib32-libxcomposite libxinerama lib32-libgcrypt libgcrypt lib32-libxinerama ncurses lib32-ncurses opencl-icd-loader lib32-opencl-icd-loader libxslt lib32-libxslt libva lib32-libva gtk3 lib32-gtk3 gst-plugins-base-libs lib32-gst-plugins-base-libs vulkan-icd-loader lib32-vulkan-icd-loader

# 提升游戏性能的程序：
yaourt -S gamemod lib32-gamemode
# 配置gamemode：
一、查找gamemode路径
find /usr -iname 'libgamemodeauto*' 
二、复制路径 
/usr/lib/libgamemodeauto.so.0
三、添加路径
运行lutris，点击preferences中的system options里面的add，
key输入LD_PRELOAD，Value输入上述路径/usr/lib/libgamemodeauto.so.0，save。
# 继续提升性能：
参照 https://github.com/lutris/https://github.com/lutris/docs/blob/master/Performance-Tweaks.md 最下面
其中，NIVDIA可按照上述添加路径的方法，
key输入__GL_THREADED_OPTIMIZATION，Value输入1;
AMD可可按照上述添加路径的方法，
key输入mesa_glthread，Value输入true，save。

# 继续提升性能：
使用带tkg的wine版本，manager version，因为带tkg的wine都启用了ESyns,对性能的提升极大;
但是要发挥ESyns的效果，需要优化内核：
sudo pacman -S ulimit
ulimit -Hn
若出现的数字比524288大（不小于就行），就不用配置了，若没有，则继续：
sudo nano /etc/systemd/system.conf
将 DefaultLimitNOFILE 前面的#去掉，= 后面的数字改为524288；
sudo nano /etc/systemd/user.conf
将 DefaultLimitNOFILE 前面的#去掉，= 后面的数字改为524288.

# 其他配置可参照https://github.com/lutris/lutris/wiki/ 修改。
