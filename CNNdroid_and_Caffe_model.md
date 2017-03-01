## CNNdroid+Caffe使用攻略 ##

0.  CNNdroid特性快速了解：http://www.jianshu.com/p/b60a216845e8
1.  资源准备：
     >github::https://github.com/ENCP/CNNdroid
     >pycaffe(后面需要用来做model转换)
     >caffe的model zoo :https://github.com/BVLC/caffe/wiki/Model-Zoo
2.  阅读《CNNdroid Complete Developers Guide and Installation Instruction》(PDF)
3.  不想读PDF英文也可以偷懒直接看这里，但是出了什么问题还是建议在PDF手册里查找。
4.  打开Parameter Generation Scripts/SavePycaffeModelInMessagePack.py，编辑里面定义的model，prototxt文件的位置，输出位置，设置一下输出参数（比如是否需要输出blob），直接运行就没太大问题了，注意依赖库
5.  在目录netfile examples/里有不同网络的样例，在这里简要介绍一下参数：
    - root_directory:指定msg文件的根目录，记得编写时在最后加上/，即
       ```"/sdcard/Data_CaffeNet/"``` √
       ```"/sdcard/Data_CaffeNet"```   ×
    - allocated_ram:指定CNNdroid可用最大内存，由于Android 规定了app最大申请的内存是512MB，所以你填再大也没用，加上在源码里CNNdroid是写死了最大值是400M，所以自己看着办。**追加一点：CNNdroid有自己的内存管理机制，PDF里说大致就是为了减小内存开销和保持IO性能的平衡，在运行时会选择一部分层常驻内存，另外一些则随用随load，用完就释放。**
    - execution_mode: 可选择并行或者串行执行。并行的话会使用CPU的多线程加速以及RenderScript来GPU加速
    - auto_tuning：用来做自动优化，调整卷积层和全连接层里，每条线程输入输出浮点数的个数。换句话说其实就是自动选择合适的生产者消费者比例和数量，在源码中也可以看到大量命名类似于InF8OutF4的方法，这里就是对应的执行8输入4输出的计算方法。这个属性一般“on”就OK了。
    - layer：其实比较坑的是这里。对比caffe的网络定义文件.prototxt,CNNdroid的网络定义文件也大同小异，只要按着网络本身每一层出现的顺序，逐一填充相应参数，就能转换个80%的定义了。剩下一些不确定的参数需要展开讲：
    >bottom,top ：在caffe的定义里有，但是在CNNdroid只要按照网络的结构顺序来填写layer就已经代表了上下层关系，所以被废弃。
    >卷积层convolution里的convolution_param {
    >num_output: 96
    >kernel_size: 7   
    >}这两个参数在经过model到msg的格式转换过程中已经包含在相应的msg文件当中，不需要填写。同理，其他CNNdorid里没有的参数，大多是被保存在相应的msg文件中，遂不需要填写。

    - 鉴于我是机器学习小白，对于很多参数的意义也不太了解，CNNdroid大概是当作适用人群有一定基础，就没有太多解释这里的参数要怎么修改，导致坑了一定时间，最后我在github上问了一下作者们，他们给了我关于这几个参数的答复：
    >- Group is used in convolution layer and in the AlexNet because of the multi-GPU tuning method they had proposed, you can just use a value of 1 for the other networks.
    >  group参数用在convolution卷积层和AlexNet 当中用于并行加速，其余的全填1就OK
    >- Normalization region is also only used in LRN layer which is present in the AlexNet but not in the other CNNs.
    >  同样CNNdroid多出来的一个参数norm_region: "across_channels"在caffe里不需要管，直接照样复制就行
    >- Dropout layer is used in the training phase, but CNNdroid is only meant to be used to run CNNs in the test phase, that's why it's not available in the library.
    >  Caffe里有dropout层是用在训练网络的时候的，但是CNNdroid只用来跑结果，所以不支持Dropout层。

6.直接运行提供的Demo.apk会有谜之BUG，多半是在安卓的调用上的问题，拍照和选择图片都不能返回数据。其实不考虑太多安卓平台上主线程的限制，只调用的话很简单，一共四步：

        try {
            // 1) Create a Renderscript object
            RenderScript myRenderScript = RenderScript.create(this);
    
            // 2) Construct a CNNdroid object
            //	  and provide NetFile location address.
            String NetFile = "/sdcard/Data_ImageNet2012/AlexNet_def.txt";
            CNNdroid myCNN = new CNNdroid(myRenderScript, NetFile);
    
            // 3) Prepare your input to the network.
            //		(The input can be single or batch of images.)
            float[][][]  inputSingle = new float[3][227][227];
            float[][][][] inputBatch = new float[16][3][227][227];
    
            // 4) Call the Compute function of the CNNdroid library
            //    and get the result of the CNN execution as an Object
            //	  when the computation is finished.
            Object output = myCNN.compute(inputSingle);
    
        } catch (Exception e) {
            e.printStackTrace();
        }*/

但是直接全写在onCreate上好像会崩，于是我丢在Asynctask里跑了，具体见待会下面的git链接。
7. 对于输入的图片数据，需要做大小调整到227*227【具体看不同的网络的要求】,条件允许可以减去均值（个人感觉能略微提高准确度，听说对计算速度也有一点点作用）
8. 返回类型是Object，需要自己根据model的真实返回值来做强制类型转换，像是age_and_gender这个model，他返回一个float[1][8]的数组，里面保存着对于该输入，分类为下标(0,i)的概率（别问我为什么实际上只有一维，我猜是做了批处理的接口吧）
9. 关于速度的对比：对比起Caffe的移植到安卓上的实现，CNNdroid无疑运行速度更快，在我的小米note（？）上大概是10s和1s的区别，然而1s还是有点不太理想，目前不知道有什么地方可以下手去加速。


