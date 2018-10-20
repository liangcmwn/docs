1、安装xrdp
sudo apt-get install xrdp
2、安装vnc4server
我这里是安装xrdp的时候自动安装的。我看网上很多说是需要单独安装的。
3、安装xfce4
sudo apt-get install xubuntu-desktop
这个软件比较大，总计需要260M。
4、配置xfce4
echo "xfce4-session" >~/.xsession
创建.xsession文件并写入内容。

这样之后，网上很多做法就已经结束了，但是我使用远程桌面连接mstsc.exe连接的时候，仅仅显示connecting to sesman ip127.0.0.1之后就没有反映了，应该继续连接ok才对。这样，继续：

5、继续配置xfce4
sudo vi /etc/xrdp/startwm.sh
在. /etc/X11/Xsession前一行插入
xfce4-session

6、重启xrdp
sudo service xrdp restart

7、使用远程桌面连接mstsc.exe，连接之后类型选择sesman-xvnc
填写用户名和密码之后就好了

注意，第4步和第5步，两个配置xfce的步骤，缺一不可。

8、修改tab键自动补全功能
至此为止，远程登录可以正常使用了，但是在终端中无法使用tab的自动补全功能，使用起来甚是不便，使用如下方法修改：
此法不需要重启系统，可以直接在远程桌面中设置，打开菜单->设置->窗口管理器，或者在终端中输入xfwm4-settings打开（xfwm4就是xfce4 window manger的缩写）
选择键盘，可以看到窗口快捷键中动作一列有“切换同一应用程序的窗口”选项，将该选项的快捷键清除后关闭窗口即可。
缺省的设置，该项的值为“super+制表”，制表键就是tab键。这样修改后马上可以使用了。

-----------------------
原文：https://blog.csdn.net/zz_1215/article/details/77921405 