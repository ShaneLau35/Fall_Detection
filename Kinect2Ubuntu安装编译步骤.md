吴伟斌Eric

Feb 13, 2017

# 安装步骤

1.    按照[这里](http://blog.csdn.net/whuhjp/article/details/50186929)的方法1安装CMake3.X，默认用apt-get安装的版本是2.X，会出现编译问题。

2.    ​

          sudo apt-get install cmake git libusb-dev freeglut3-dev pkg-config build-essential libxmu-dev libxi-dev libusb-1.0-0-dev libgtk2.0-dev python-dev python-numpy libavcodec-dev libavformat-dev libswscale-dev git-core 

3.    按照教程[安装libfreenect](https://openkinect.org/wiki/Getting_Started#Ubuntu_Manual_Install)安装

            其中
            sudo ldconfig /usr/local/lib64/
            换成
            sudo ldconfig /usr/local/lib/
            把下面这句加到~/.bashrc，记得要换成自己的路径
            export LD_LIBRARY_PATH=/home/forrest/libfreenect/build/lib/

4.    进入OpenNi2

           sudo ./install

5.    按照[OpenNI2-FreenectDriver](https://github.com/OpenKinect/libfreenect/tree/master/OpenNI2-FreenectDriver)教程。最后在目录`OpenNI-Linux-x64-2.2/Redist/OpenNI2/Drivers/`和 `NiTE-Linux-x64-2.2/Samples/Bin/OpenNI2/Drivers/` 也cp -L一份libFreenectDriver.so

6.    进入NiTE

           sudo ./install

       到这里安装就完成了

# 编译前的一些准备

1. OPENNI2\_INCLUDE, OPENNI2_REDIST, NITE2\_INCLUDE, NITE2\_REDIST环境变量在~/.bashrc设置好，如

   ```
   export  OPENNI2_INCLUDE=… 
   export  OPENNI2_REDIST=… 
   ...
   ```

   这几个变量的目录就在OpenNi和Nite的根目录下的Include和Redist

2. `sudo ln -s Nite目录/Redist/libNiTE2.so /usr/lib/libNiTE2.so`

3. ```
   $ sudo gedit /etc/ld.so.conf.d/openni2.conf
   加入
   /<your-custom-shared-lib-location>/Redist/

   $ sudo gedit /etc/ld.so.conf.d/nite2.conf
   加入
   /<your-custom-shared-lib-location>/Redist/
   ```

4. 现在OpenNi中Samples的Recorder和Nite中Samples的UserViewerVideo都应该可以编译了，这两个的Makefile我都做过修改，可以直接make。

   Recorder：运行既即开始录制视频，按任意键结束，名字为test.oni，如果看到图标有锁，需改权限才能访问。

   Userviewervideo: 在UserViewer上做了修改，命令行后面加入.oni文件的路径就可以对视频做检测，terminal会输出头和躯干的高度。


# OpenNi2 + libfreenect + Nite2

# 安装遇到的问题

1. 把cmake更新到3以上。

2. 运行Nite Samples: Failed to open device DeviceOpen using default: no devices found。把libfreenectdriver.so 的文件用 cp -L 从libfreenect中拷过去，不能直接拷。

3. OPENNI2\_INCLUDE, OPENNI2_REDIST环境变量设置好才能编译Openni2的Samples

4. 加入NITE2\_INCLUDE, NITE2\_REDIST环境变量，Nite2编译要改makefile

   INC_DIRS 加上./ 

   LIB_DIRS加上$(NITE2_REDIST)

5. -lNiTE错误，用ln连接libNiTE2.so到/usr/lib

6. ```
   sudo gedit /etc/ld.so.conf.d/openni2.conf

   /<your-custom-shared-lib-location>/Redist/
   ```

   Note you only need to add the path and **not** the PATH variable in the file. Then run

   ```
   sudo ldconfig
   ```

   以上是`libopenni2.so: cannot open shared object file`解决

7. ```
   Could not find data file ./NiTE2/s.dat
   ```

   Copy the directory ~/Downloads/NiTE-MacOSX-x64-2.2/Redist/NiTE2 into what Xcode lists as its current working directory