carlifeLib使用说明

第一章 编译环境和硬件环境
1.编译环境
1.一个编译通过的 wandboard4.4.2Android环境
	pc环境为Ubuntu14.04，在用户目录下建/work/proj目录，将wandboard4.4.2源码解压到proj目录下，注意磁盘要大于100G，最好是将一个足够大的硬盘挂载在home目录下。（编译完有50G）
	打开终端依次安装以下依赖库
	sudo apt-get install git gnupg flex bison gperf build-essential zip curl libc6-dev libncurses5-dev:i386 x11proto-core-dev libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-dri:i386 libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown libxml2-utils xsltproc zlib1g-dev:i386 dpkg-dev
	sudo ln -s /usr/lib/i386-linux-gnu/mesa/libGL.so.1 /usr/lib/i386-linux-gnu/libGL.so

2.安装Oracle JDK
注意：如果编译Android4.4不能用OpenJDK，必须用Oracle JDK1.6，1.7目前是不支持的。
如果装OPenJDk 就卸载掉。
	去oracle官网下载”Java SE Development Kit 6u45“，解压然后将路径加载到用户目录下的./bashrc 中。
	 配置ubuntu的JDK和JRE的位置，配置Oracle为系统默认JDK/JRE。
	然后用 Java -version 查看是否安装成功。
$ java -version
java version "1.6.0_45"
Java(TM) SE Runtime Environment (build 1.6.0_45-b06)
Java HotSpot(TM) 64-Bit Server VM (build 20.45-b01, mixed mode)

Android源码编译环境安装好后，进入源码根目录，运行命令
$source build/envsetup.sh
配置好后，运行命令
$lunch wandboard-userdebug
$meke -j4
编译大概需要三个小时。。。。

3.carlifelib还需要另外依赖几个库：
		在这里需要将包中的protobuf和dropbear放入/external 目录下用mm命令进行编译，先编译dropbear后编译protobuf
	编译完后将修改好的carlifelibsourcecode添加到/external下用mm，命令进行编译

注意：/external 原来就有protobuf和dropbear，原来的protobuf和dropbear要保留	
如果命令mm编译不成功，cd到根目录运行
	&	. build/envsetup.sh
	&	lunch wandboard-userdebug
再cd到要编译的目标目录下，mm编译。
	
2.硬件环境
1.开发板是wandboard，语音识别时需要接一个麦克风
2.安卓手机,需要安装百度carlife软件


第二章	carlifelib源码使用
代码在carlifelibsourcecord中
1.CarLifebin.cpp是Linux环境下的carlifeLib的测试demo，在Android.mk中对它进行编译。
2.CarLifejni.cpp是安卓app调用carlifeLib库的jni文件。
3.nfMirAPI.cpp是app调用c/c++函数的注册文件。
4.剩下的代码结构和库函数使用可以参考CarLife动态链接库使用说明.docx


第三章 apkdemo的使用
软件环境：
1.eclipse
2.jdk java -version 1.6.0_45
3.因为软件权限的原因需要将app代码码放到Android源码/pakage/app下进行编译
4.详细的通讯协议和时序图可以参考百度carLife车机手机互联方案接入详细指南new.pdf

	整个demo主要设置一个视屏播放线程，三个语音播放线程，一个录音线程，视屏线程播放手机传过来的视屏流，一个语音线程播放手机传过来的media数据流，一个语音线程播放导航，一个播放语音识别语音。录音线程，在语音识别是启动，将录制的数据发送手机，通过手机进行识别。
	程序运行的一部分信息通过logcat查看。

注意：
1.这个demo在运行时需要获取apk的adb  root权限，注意不是手机的adb root权限，如果adb已经启动，就杀死它，用 adb root命令启动 exit退出，用ps查看，adb是否为root。
root@wandboard:/ # ps adb
USER     PID   PPID  VSIZE  RSS     WCHAN    PC         NAME
root      1459  1     3564   4     ffffffff 00018f00 S /sbin/adbd
system    4124  1     3008   888   ffffffff 2abcd7b0 S adb
root@wandboard:/ # kill 4124
root@wandboard:/ # [237395.556930] init: untracked pid 4124 exited
root@wandboard:/ # adb root
shell@wandboard:/ $ exit 
root@wandboard:/ # ps adb                                                  
USER     PID   PPID  VSIZE  RSS     WCHAN    PC         NAME
root      1459  1     3564   4     ffffffff 00018f00 S /sbin/adbd
root      4184  1     5084   888   ffffffff 2ac3d7b0 S adb
root@wandboard:/ # logcat


2.这个demo是放在wandboard上与Samsung note4上进行测试的，其他板或手机可能会有不兼容问题。
3.不管是要做什么，比如联网，读写文件等，都要在AndroidManifest.xml添加相应的权限。
 <uses-permission android:required="true" 	android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"></uses-permission>



工作环境：
	1.ubuntu14.04
	2.3.5版本的sourceinsight
	3.eclipse
	4.gedit
	5.adb
