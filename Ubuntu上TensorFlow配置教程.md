###　Ubuntu上配置TensorFlow
* Ubuntu：版本 14.0.4
* CUDA： 8.0
* TensorFlow 官方配置教程	：

		https://www.tensorflow.org/install/install_linux#python_34
* CUDA官方配置教程

		http://docs.nvidia.com/cuda/cuda-installation-guide-linux/#axzz4VZnqTJ2A

* 在安装TensorFlow之前先保证已经配置好了cuda

### CUDA安装教程

* 预安装操作
	* 使用给定的命令去检查本机是否具备支持CUDA的GPU，如果没有支持就在后面的安装中选择***“仅支持CPU”***的选项
	* 每一步执行时，CUDA文档都给了不同版本的Linux型号安装的命令。要对照自己的需求选择命令。比如Ubuntu系统的就使用Ubuntu下给的命令。
	* 文档中***“2.4 验证系统是否正确安装了内核标头和开发包”比较关键***
		* 通过类似于 wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1404/x86_64/cuda-repo-ubuntu1404_6.5-14_amd64.deb  来获取内核标头
		* 关键在替换"/cuda-repo-ubuntu1404_6.5-14_amd64.deb"成自己需要的版本号
		* 而后执行 sudo dpkg -i cuda-repo-<distro>_<version>_<architecture>.deb   把  cuda-repo-ubuntu1404_6.5-14_amd64.deb
		* 具体可以参考：http://blog.csdn.net/xizero00/article/details/43227019 关于这一步的配置

* 而后就直接更新并安装CUDA就可以了
* 安装完成后注册环境设置。
* 本教程使用Package Manager Installation法安装的，注册环境变量时候只需要执行相关注册命令就可以。

### TensorFlow 安装教程
 * 使用本地pip安装
	 * 安装Python和Pip
	 * 安装TensorFlow
	 * 拿到国内的TensorFlow镜像 ：https://mirrors.tuna.tsinghua.edu.cn/help/tensorflow/
	 * sudo pip 替换成国内镜像中拿到的结果