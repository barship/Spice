```
# 主要工作:

## 1. 安装配置spice-gtk;

​```sh
虚拟机环境-VMware Workstation Pro 16
虚拟机镜像-Debian10.10.0-amd64
VMnet8子网地址 192.168.75.0
1)配置ip及dns服务器:
root@bogon:/home/rocky#vi /etc/network/interfaces
auto lo
iface lo inet loopback
auto ens33
iface ens33 inet dhcp

root@bogon:/home/rocky#vi /etc/resolv.conf
domain localdomain
search localdomain
nameserver 192.168.75.2

root@bogon:/home/rocky#/etc/init.d/networking restart
2)apt源配置: root@bogon:/home/rocky#vi /etc/apt/sources.list
输入下述信息:
deb https://mirrors.bfsu.edu.cn/debian buster main
deb https://mirrors.bfsu.edu.cn/debian buster-updates main
deb https://mirrors.bfsu.edu.cn/debian buster-backports main

root@bogon:/home/rocky#apt update
3)编译环境需要的依赖包:
spice-protocol spice-protocol-0.14.3
spice-gtk spice-gtk-0.39
meson & ninja的获取和使用
root@bogon:/home/rocky/Spice#apt install ninja-build
root@bogon:/home/rocky/Spice#git clone https://gitlab.com/qemu-project/meson.git
编译最小功能的spice-gtk的步骤:
先编译spice-protocol:
root@bogon:/home/rocky/Spice/spice-protocol-0.14.3#/home/rocky/Spice/meson/meson.py build
root@bogon:/home/rocky/Spice/spice-protocol-0.14.3#cd build
root@bogon:/home/rocky/Spice/spice-protocol-0.14.3#/home/rocky/Spice/meson/meson.py configure -Dprefix=/home/rocky/compile
root@bogon:/home/rocky/Spice/spice-protocol-0.14.3#ninja && ninja install
root@bogon:/home/rocky/Spice/spice-protocol-0.14.3#export PKG_CONFIG_PATH:/home/rocky/compile/share/pkgconfig
然后编译spice-gtk,步骤同上;
编译成功的汇总信息:
spice-gtk 0.39
    prefix                   :/usr/local
    opus                     :NO
    gtk                      :NO
    webdav                   :NO
    builtin-mjpeg            :YES
    usbredir                 :NO
    libcap-ng                :NO
    polkit                   :NO
    pie                      :YES
    usb-acl-helper-dir       :/usr/local/libexec
    usb-ids-path
    coroutine                :ucontext
    introspection            :NO
    vapi                     :NO
    lz4                      :NO
    sasl                     :NO
    smartcard                :NO
    valgrind                 :NO
    libspice-client-glib.so version: 8.7.0
    gtk-doc                  :NO

Subprojects
    keycodemapdb             :YES
    spice-common             :YES 2 warnings

Found ninja-1.8.2 at /usr/bin/ninja
最小安装后启用功能为builtin-mjpeg pie;
安装后/home/rocky/compile的bin目录下只有spicy-stats和spicy-screenshot无spicy
4)出现的问题及解决方案
i)meson编译报错:
meson.build:4:0 ERROR:Unknown compiler(s):[['cc'].['gcc'],['clang'],['nvc'],['pgcc'],['icc']]
The following exception(s) were encountered:
Running "cc --version" gave "[Errno 2]No such file or directory: 'cc':'cc'"
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install gcc
ii)meson编译报错:
subprojects/spice-common/meson.build:110:2:ERROR:Pkg-config binary for machine 1 not found Giving up.
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install pkg-config
iii)meson编译报错:
subprojects/spice-common/meson.build:110:2:ERROR:Dependency "glib-2.0" not found,tried pkgconfig
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install libssl1.1=1.1.1n-0+deb10u2 zlib1g-dev=1.2.11.dfsg-1+deb10u1_amd64 libglib2.0-dev libssl-dev libspice-client-glib-2.0-dev
iv)meson编译报错:
subprojects/spice-common/meson.build:137:8:ERROR:Problem encountered:Python module pyparsing not found
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install python3-pyparsing
v)meson编译报错:
subprojects/spice-common/meson.build:119:2:ERROR:Dependency "cairo" not found,tried pkgconfig
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install libcairo2-dev
vi)meson编译报错:
subprojects/spice-common/meson.build:119:2:ERROR:Dependency "libjpeg" not found,tried pkgconfig
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install libjpeg-dev
vii)meson编译报错:
subprojects/spice-common/meson.build:119:2:ERROR:Dependency "json-glib-1.0" not found,tried pkgconfig
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install libjson-glib-dev
viii)meson编译报错:
subprojects/spice-common/meson.build:205:2:ERROR:Dependency "gstreamer-1.0" not found,tried pkgconfig
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install libgstreamer-1.0-dev
viiii)meson编译报错:
subprojects/spice-common/meson.build:205:2:ERROR:Dependency "gstreamer-app-1.0" not found,tried pkgconfig
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install libgstreamer-plugins-base1.0-dev
​```

​```sh
编译功能齐全的的spice-gtk的步骤:
先编译安装完最小功能的spice-gtk,解决所有问题后,接着解决功能依赖库的安装问题:
出现的问题及解决方案
i)meson编译警告:
(spice-common)Run-time dependency opus found:NO(tried pkgconfig and cmake)
[解决方法]
root@bogon:/home/rocky#apt install libopus-dev
ii)meson编译警告:
(spice-common)Run-time dependency libcacard found:NO(tried pkgconfig and cmake)
[解决方法]
root@bogon:/home/rocky#apt install libcacard-dev
iii)meson编译警告:
Run-time dependency libphodav-2.0 found:NO(tried pkgconfig and cmake)
[解决方法]
root@bogon:/home/rocky#apt install libphodav-2.0-dev libxml2-dev=dfsg1-7+deb10u4 libsoup2.4-dev
iv)meson编译警告:
Run-time dependency libcap-ng-dev found:NO(tried pkgconfig and cmake)
[解决方法]
root@bogon:/home/rocky#apt install libcap-ng-dev
v)meson编译警告:
Run-time dependency liblz4 found:NO(tried pkgconfig and cmake)
[解决方法]
root@bogon:/home/rocky#apt install liblz4-dev
vi)meson编译警告:
Run-time dependency libsasl2 found:NO(tried pkgconfig and cmake)
[解决方法]
root@bogon:/home/rocky#apt install libsasl2-dev
vii)meson编译警告:
Run-time dependency gobject-introspection-1.0 found:NO(tried pkgconfig and cmake)
[解决方法]
root@bogon:/home/rocky#apt install libgirepository1.0-dev
viii)meson编译警告:
Run-time dependency libusbredirhost found:NO(tried pkgconfig and cmake)
Run-time dependency libusbredirparser-0.5 found:NO(tried pkgconfig and cmake)
Run-time dependency libusb-1.0 found:NO(tried pkgconfig and cmake)
root@bogon:/home/rocky#apt install libredirhost-dev libredirparser-dev libusb-1.0-0-dev
viiii)meson编译警告:
Run-time dependency vapigen found:NO(tried pkgconfig and cmake)
[解决方法]
root@bogon:/home/rocky#apt install valac
vv)meson编译警告:
Has header "valgrind/valgrind.h":NO
[解决方法]
root@bogon:/home/rocky#apt install valgrind
vvi)meson编译警告:
Run-time dependency polkit-gobject-1 found:NO(tried pkgconfig and cmake)
[解决方法]
root@bogon:/home/rocky#apt install libpolkit-gobject-1-dev
vvii)安装完polkit-gobject-1后meson编译报错:
meson:build:251:4:ERROR:C shared or static library 'acl' not found
A full log can be found at /home/rocky/Spice/spice-gtk-0.39/build/meson-logs/meson-log/txt
[解决方法]
root@bogon:/home/rocky#apt install libacl1-dev
vvi)meson编译警告:
Run-time dependency gtk+-3.0 found:NO(tried pkgconfig and cmake)
[解决方法]
root@bogon:/home/rocky#apt install libspice-client-gtk-3.0-dev
vviiii)安装完gtk+-3.0后meson编译报错:
meson:build:177:4:ERROR:Invalid version of dependency,read 'wayland-server' ['>=1.17.0'] found '1.16.0'
A full log can be found at /home/rocky/Spice/spice-gtk-0.39/build/meson-logs/meson-log/txt
[解决方法]
root@bogon:/home/rocky#apt install libffi7=3.3-6 libwayland-server0=1.18.0-2~exp1.1 libwayland-client0=1.18.0-2~exp1.1 libwayland-cursor0=1.18.0-2~exp1.1 libwayland-egl1=1.18.0-2~exp1.1 libwayland-bin=1.18.0-2~exp1.1 libwayland-dev=1.18.0-2~exp1.1

配置环境与keycodemapdb不兼容,meson编译报错:
meson.build:53:0:ERROR:Sandbox violation:Tried to grab file keymap-gen from nested subproject
A full log can be found at /home/rocky/Spice/spice-gtk-0.39/build/meson-logs/meson-log/txt
注释掉keymapgen和keymapcsv后可以进行不完全安装,编译成功的汇总信息:
spice-gtk 0.39
    prefix                   :/usr/local
    opus                     :YES
    gtk                      :NO
    webdav                   :YES
    builtin-mjpeg            :YES
    usbredir                 :YES
    libcap-ng                :YES
    polkit                   :YES
    pie                      :YES
    usb-acl-helper-dir       :/usr/local/libexec
    usb-ids-path
    coroutine                :ucontext
    introspection            :YES
    vapi                     :YES
    lz4                      :YES
    sasl                     :YES
    smartcard                :YES
    valgrind                 :NO
    libspice-client-glib.so version: 8.7.0
    gtk-doc                  :NO

Subprojects
    keycodemapdb             :YES
    spice-common             :YES 2 warnings

Found ninja-1.8.2 at /usr/bin/ninja
但是在安装完gtk+-3.0和wayland后meson编译报错:
src/meson.build:315:4:ERROR:Unknown variable "keymapgen"

vvv)meson编译警告:
Has header "epoxy/egl.h":NO
[解决方法]
root@bogon:/home/rocky#apt install libwayland-egl1=1.18.0-2~exp1.1
vvvi)meson编译警告:
Program gtkdoc-scan found:NO
[解决方法]
root@bogon:/home/rocky#apt install gtk-doc-tools
​```

​```sh
虚拟机环境-VMware Workstation Pro 16
虚拟机镜像-linxos-6.0.100-20210922-amd64 http:://www.linx-info.com/#/support/software
VMnet8子网地址 192.168.75.0
配置ip及dns服务器、apt源配置、编译环境需要的依赖包、meson & ninja的获取和使用及编译最小功能和功能完全的spice-gtk的步骤同debian10;
最小功能的spice-gtk编译成功的汇总信息:
spice-gtk 0.39
    prefix                   :/usr/local
    opus                     :NO
    gtk                      :NO
    webdav                   :NO
    builtin-mjpeg            :YES
    usbredir                 :NO
    libcap-ng                :NO
    polkit                   :NO
    pie                      :YES
    usb-acl-helper-dir       :/usr/local/libexec
    usb-ids-path
    coroutine                :ucontext
    introspection            :NO
    vapi                     :NO
    lz4                      :NO
    sasl                     :NO
    smartcard                :NO
    valgrind                 :NO
    libspice-client-glib.so version: 8.7.0
    gtk-doc                  :NO

Subprojects
    keycodemapdb             :YES
    spice-common             :YES 2 warnings

Found ninja-1.8.2 at /usr/bin/ninja
最小安装后启用功能为builtin-mjpeg pie(coroutine glib);
1)出现的问题及解决方案
i)meson编译报错:
subprojects/spice-common/meson.build:137:8:ERROR:Problem encountered:Python module six not found
A full log can be found at /home/rocky/Spice/spice-gtk-0.39/build/meson-logs/meson-log.txt
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#pip3 install six
ii)meson编译报错:
subprojects/spice-common/meson.build:137:8:ERROR:Problem encountered:Python module pyparsing not found
A full log can be found at /home/rocky/Spice/spice-gtk-0.39/build/meson-logs/meson-log.txt
iii)meson编译报错:
subprojects/spice-common/meson.build:137:8:ERROR:Problem encountered:Python module six not found
A full log can be found at /home/rocky/Spice/spice-gtk-0.39/build/meson-logs/meson-log.txt
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install python3-pyparsing
iii)meson编译报错:
subprojects/spice-common/meson.build:119:2:ERROR:Dependency "json-glib-1.0" not found,tried pkgconfig
[解决方法]
root@bogon:/home/rocky/Spice/spice-gtk-0.39#apt install libjson-glib-dev
linx100中预安装的包比debian10.10多,很多依赖包不需要安装;
编译功能齐全的spice-gtk时出现的问题及解决方案同debian10;
2)启用每个功能需要安装或编译的软件包及支持功能介绍;
    opus                     :libopus-dev 
                              Opus codec library development files; 
                              提供音频流编解码;
    gtk                      :libspice-client-gtk-3.0-dev 
                              GTK3 widget for SPICE clients (development files); 
                              提供spice客户端gtk开发插件用于图形用户界面;
    webdav                   :libphodav-2.0-dev 
                              WebDAV server implementation 
                              using libsoup (development files);
                              提供基于HTTP 1.1协议(libsoup实现)的webDAV服务器实现,
                              使应用程序可对Web Server直接读写,
                              并支持写文件锁定(Locking)及解锁(Unlock),还可以支持文件的版本控制;
                             :libsoup2.4-dev 
                              HTTP library implementation in C -- Development files;
    builtin-mjpeg            :libjpeg-dev 
                              Development files for the JPEG library [dummy package]; 
                              提供基于JPEG的图像编解码;
    usbredir                 :libusb-1.0-0-dev 
                              userspace USB programming library development files;
                              libusbredirhost-dev 
                              implementing the usb-host (*) side of a usbredir 
                              connection (development);
                              libusbredirparser-dev 
                              Parser for the usbredir protocol (development);
                              Spice can be used to redirect USB devices that are 
                              plugged in the client to the guest OS.
                              This redirection can either be automatic (all newly 
                              plugged devices are redirected),or manual (the user 
                              selects which devices (s)he wants to redirect).
    libcap-ng                :libcap-ng-dev 
                              Development and header files for libcap-ng;
                              提供一些实用程序,
                              可以分析所有当前正在运行的应用程序并打印出任何功能
                              以及是否具有开放式边界集,
                              常见功能包括删除所有功能、保留一项功能、保留多项功能、
                              检查是否有任何功能、检查某些功能和在uid更改中保留功能;
    polkit                   :libpolkit-gobject-1-dev 
                              PolicyKit Authorization API - development files;
                              gir1.2-polkit-1.0 
                              GObject introspection data for PolicyKit;
                              libpolkit-agent-1-0 
                              PolicyKit Authentication Agent API;
                              提供Polkit授权管理;
                              根据预先设定好的配置来确定是否要求额外的权限,
                              需要额外的权限会去向libpolkit验证当前操作是否获得了权限,
                              如果当前调用已经取得权限了,则调用顺利通过,否则返回无权限的提示或异常;
    pie                      :?(gnome-pie?自带YES) 
                              visual application launcher for GNOME; 
                              提供GNOME可视化应用启动器
    introspection            :libgirepository1.0-dev 
                              Library for handling GObject introspection 
                              data (development files);
                              提供基于GObject的native library能轻易porting
                              到script language或高阶语言,即binding功能;
    vapi                     :valac 
                              C# like language for the GObject system;
                              提供
    lz4                      :liblz4-dev 
                              Fast LZ compression algorithm library - development files; 
                              提供基于lz压缩算法的图像编解码;
    sasl                     :libsasl2-dev 
                              Cyrus SASL - development files for 
                              authentication abstraction library;
                              提供客户端与服务端连接和消息传递时的身份认证;
    smartcard                :libcacard-dev 
                              Virtual Common Access Card (CAC) 
                              Emulator (development files);
                              data from smartcard that are inserted into the 
                              client machine can be passed through to the guest OS.
                              The smartcard can be used by both the client OS 
                              and the guest OS.
    valgrind                 :?(apt安装valgrind后仍显示NO) 
                              instrumentation framework for building dynamic 
                              analysis tools;
    gtk-doc                  :gtk-doc-tools 
                              GTK+ documentation tools;
                              提供gtk接口文档;

​```

​```
任务完成情况




```

