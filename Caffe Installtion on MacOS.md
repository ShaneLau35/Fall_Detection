# Caffe Installtion on MacOS


###author:臧若舟 email:zrzn@foxmail.com

### (GPU  is not recommended)


###If you insist on using gpu on mac(make sure your gpu is Nividia g650 or g750, cuda doesn't support AMD gpu.)

----------

* You can stay with python2.7 if you are used to it, which can reduce the degree of difficulty.

* If you are using python 3.3+, make sure you install any libraries with python3, otherwise you may fail to config caffe.

##Here is the steps

1.

`brew install -vd snappy leveldb gflags glog szip lmdb`

`brew tap homebrew/science`

`brew install hdf5`
	
2.(different from the official website)

`brew install opencv3 --with-python3 --c++11 --with-contrib`

`brew link --force opencv3`

check the opencv installation by `import cv2` in python3.

3.(different from the official website)

`brew install --build-from-source --with-python -vd protobuf`
  
`brew install boost boost-python --with-python3`
  check in /usr/local/Cellar/boost-python/1.63.0/lib, make sure the 'libboost_python3.a' is included.
 
4.git clone caffe to your local and follow <http://git.curi.hk/ZRZn/FaceRecognitionCNN/blob/master/Makefile.config> to config the Makefile.config.

5.don't forget to config environment variable by adding
`export PYTHONPATH=/usr/local/lib/python3.5/site-packages/caffe/python:$PYTHONPATH` '/usr/local/lib/python3.5/site-packages/' is your own caffe path, but copy the caffe to the path the same as mine is recommended.
  
6.

`cp Makefile.config.example Makefile.config`

`make all`

`make test`

`make runtest`

7.when you finish all of this, but still fail to import caffe in python3, check the python-dateutil's version is over 2.0(the newest is 2.6.0 up to 2017.2.28). You can download it from <https://pypi.python.org/pypi/python-dateutil> and run 'setup.py' to install it. If this remain useless, copy python-dateutil-2.x/dateutil to your /usr/local/...