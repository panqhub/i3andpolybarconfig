#### 安装前提
1. 装好的Archlinux
2. 可以正常连接网络
#### 安装步骤
1. 安装xorg-server和xorg-xinit
xorg-server是x window的实现，是用于显示图形界面。
```
pacman -S xorg-server xorg-xinit
```
如果想从xinit启动i3，那么就装上xorg-xinit，如果你想通过显示管理器启动，那么可以不装（本教程展示用xinit启动的方式，用显示管理器也差不多一样）

2. 安装依赖zsh、xfce4-terminal、feh、compton
zsh是个人推荐使用的shell，如果你想用默认的shell，这个可以不装
terminal这里用的是xfce4-terminal，个人建议用它，不过有一个小问题，使用ranger预览图片的时候会卡图片的显示缓存
feh用来设置背景图的
compton用来透明，这个必须装，因为polybar也依赖它
i3-gaps是i3wm的一个分支，提供窗口间隙设置等特性
```
pacman -S zsh xfce4-terminal feh compton i3-gaps
```

3. 安装你喜欢的英文和中文字体
中文这里推荐思源宋体，英文随意
```
pacman -S adobe-source-han-sans-cn-fonts
pacman -S adobe-source-han-sans-tw-fonts
pacman -S adobe-source-han-sans-jp-fonts
```

4. 创建用户
```
useradd -m -g users -G wheel -s /bin/zsh username
```
这里需要注意两点：第一，-G指定的附加组要有使用sudo的权利。第二，-s 制定用户默认的shell

5. 切换用户，并拷贝xinitrc文件到用户目录
```
su username
sudo cp /etc/X11/xinit/xinitrc ~/.xinitrc
```
这里注意拷贝后的文件改名为.xinitrc

6. 在.xinitrc添加如下内容
```
exec compton -b &
exec fcitx &                                     
exec i3 -V >> ~/.config/i3/log/i3log-$(date +'%F-%k-%M-%S') 2>&1
```
7. 重启后startx进入图形界面

8. 安装polybar
建议直接从AUR中安装
```
yaourt -S polybar
```
注意：在通过yaourt安装过程中，先查看构建包，结构如下

![2018-02-03-003608_940x1012_scrot.png](http://upload-images.jianshu.io/upload_images/6948320-f1e73bc059f71723.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
根据需要装上必要的依赖，建议都装上
要使用mpd模块的话，不仅要装上libmpdlient还要装上mpd再构建polybar

9. 从我的仓库中拷贝我的配置文件到home目录对应的配置目录
```
git clone https://github.com/AshNobita/i3andpolybarconfig.git
```
把i3andpolybarconfig文件夹下的文件按照原有的目录结构拷贝到当前用户目录下

10. 修改.xinitrc，添加如下内容
```
exec mpd ~/.config/mpd/mpd.conf 
```
11. 安装上字体图标
```
yaourt -S otf-font-awesome
yaourt -S ttf-material-icons 
```

12. 重启后在进入
你会发现音乐模块图标并没有显示，不用担心，因为你的目录里没有文件，音乐目录默认为当前用户目录下的Music文件夹，你可以通过修改.config/mpd/mpd.conf文件来改变这个路径。

13. 安装ncmpccp
```
sudo pacman -S ncmpcpp
```
安装完后命令行键入ncmpcpp进入，你会发现播放目录是空的，不用担心，看到下面的提示，正常连接上的话，一进入下面会显示connected to xxxx，然后按u刷新音乐数据库，接着按2浏览数据库文件，选择播放即可。这时候polybar上的音乐模块也正常显示了。

14. 安装i3lock-fancy
```
yaourt -S i3lock-fancy-git
```
这是i3lock的修改，用起来像下图
![2018-02-03-005814_1920x1080_scrot.png](http://upload-images.jianshu.io/upload_images/6948320-9b1bd51e148c8f2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
但因为改锁屏方式进行了一些图像处理，所以比较费事，从你按下锁屏热键到锁上屏需要3s左右的时间，如果你不能忍受这个速度，那么你可以直接使用i3lock。
在我的配置中，绑定了i3lock-fancy的锁屏快捷键为$mod+l，并设置好了定时自动锁屏，你可以自行修改。

15. 安装oh my zsh
```
pacman -S oh-my-zsh-git
```
这是配置得比较好的zsh，直接装上用就行了，由于我的配置文件中也提供了.zshrc，你可以通过浏览[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)选择你喜欢的shell配置

16. 安装ranger
```
pacman -S ranger
```
ranger是一个命令行的文件浏览器，但是如果你要使用改文件浏览器器预览图片，在大多数的终端中，可能会出现一系列显示问题。
比如配合compton透明化处理，那么用ranger预览图片可能会导致预览图片窗全部透明，在很多终端上可能出现图像刷新不正常的情况。
据ranger的作者说明，似乎是因为ranger使用的w3m生成缩略图的问题，但该项目已经很久前就停止更新了，经过我个人测试，linux终端中，目前已知的只有urxvt正常工作，相比其他终端，问题小点，但会无规律出现图片刷新显示失败。
然而urxvt的Unicode字符显示不全，中文字体渲染也比较糟糕，所以我目前并没有找到完美的解决方案。
可以根据自己的喜好和缺陷的接受程度，选择运行ranger的终端。

### 总结
i3wm是一个平铺窗口管理器，使用i3wm不仅比装上桌面环境更剩资源，而且能让你的操作更高效，配置好了也非常美观！
最终效果如下图：
![2018-02-03-010531_1920x1080_scrot.png](http://upload-images.jianshu.io/upload_images/6948320-9744d0553d3430d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


