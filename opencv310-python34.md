# Install python3.4 + openCV 3.1.0

Based on this tutorial by Adrian Rosebrock: http://www.pyimagesearch.com/2015/07/20/install-opencv-3-0-and-python-3-4-on-ubuntu/

This guide sets up:
* python3.4
* openCV 3.1.0
* opencv_contrib 3.1.0
* scikit-learn, scipy, scikit-image
* ... and gets it to work in a virtualenv.

Make sure to have about 20GB of disk space, since the build and installation takes alot of room. Consider this if you intend to perform this on a virtual computer with limited space available.

## Your OS requirements
This is known to work on Ubuntu 14.04 LTS (Trusty) AND Ubuntu 15.10 (wily)
which also allows for this to be used in conjuction with
NVIDIA DIGITS (https://developer.nvidia.com/digits) for machine learning and deep neural networking.


## Install some important dependencies for python3
```
sudo apt-get install git
sudo apt-get install python3-pip
sudo apt-get install python3-all-dev
sudo apt-get install virtualenvwrapper
sudo apt-get install python3-numpy
sudo apt-get install cmake
```
At this point, we have a working python3 development stack and virtualenv.
But lets pull in some more libs we are dependent on before we start working inside a virtual env.

## Install dependencies for scipy (BLAS/LAPACK) & mathplotlib
```
sudo apt-get install libblas-dev liblapack-dev libatlas-base-dev gfortran
sudo apt-get install libjpeg8-dev libfreetype6-dev 
sudo apt-get install libpng12-dev
sudo apt-get install libjpeg8-dev libfreetype6-dev libpng12-dev 
(recommended)
sudo apt-get install libagg-dev
```
Now, lets build a virtual environment and fill it with useful stuff.

Create a virtual env and point to python 3.4 (as your own user, don't be root)
```
virtualenv -p $(which python3.4) myvirtualenv
```

In case you miss python 3.4, read the blogg: http://devmartin.com/blog/2016/04/creating-a-virtual-environment-with-python3.4-on-ubuntu-16.04-xenial-xerus/

Install packages into the virtual env
```
source myvirtualenv/bin/activate
pip install matplotlib
pip install numpy
pip install scipy
pip install scikit-learn
pip install cython
pip install -U scikit-image
```
## Install openCV 3.1.0

From http://docs.opencv.org/2.4/doc/tutorials/introduction/linux_install/linux_install.html#getting-the-latest-stable-opencv-version

## Prepare your OS with dependencies needed for compilation.
```
sudo apt-get install build-essential
sudo apt-get install cmake libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
(optional)
sudo apt-get install libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
```
We will perform the installation into your home directory.
This simplify changes for openCV versions and upgrades later on.
Naming the "install path" same as the source git tag is a good tactic too.
The "install path" will hence be something like: ~/opencv3.1.0

## Get openCV from github at tag: 3.1.0
```
mkdir ~/git
cd ~/git
git clone git@github.com:Itseez/opencv.git
cd opencv
git checkout 3.1.0
```
Also get the opencv_contrib repo (with the same tag number) that adds great community feature modules.
```
cd ~/git
git clone https://github.com/Itseez/opencv_contrib.git
cd opencv_contrib
git checkout 3.1.0
```

Now, create the installation target directory (from your virtual env)
and configure the build.

If you are on ubuntu 15.04 or higher, you need to install gcc-4.7 and g++-4.7
since openCV 3.1.0 will fail to compile on gnu compilers 4.9+
```
sudo apt-get install g++-4.7 gcc-4.7
export CC=$(which gcc-4.7)
export CXX=$(which g++-4.7)
```

Activate the virtualenv and cmake.
```
source myvirtualenv/bin/activate
mkdir ~/opencv3.1.0
cd ~/git/opencv/
mkdir release
cd release
cmake -D CMAKE_BUILD_TYPE=RELEASE \
	-D CMAKE_INSTALL_PREFIX=~/opencv3.1.0 \
	-D INSTALL_C_EXAMPLES=OFF \
	-D INSTALL_PYTHON_EXAMPLES=ON \
	-D OPENCV_EXTRA_MODULES_PATH=~/git/opencv_contrib/modules \
	-D BUILD_EXAMPLES=ON ..
```
You should see something like:
```
--   Python 2:
--     Interpreter:                 /usr/bin/python2.7 (ver 2.7.6)
-- 
--   Python 3:
--     Interpreter:                 /home/erik/myvirtualenv/bin/python3.4 (ver 3.4.3)
--     Libraries:                   NO
--     numpy:                       /home/erik/myvirtualenv/lib/python3.4/site-packages/numpy/core/include (ver 1.11.0)
--     packages path:               lib/python3.4/site-packages
-- 
--   Python (for build):            /usr/bin/python2.7
```
If the Interpreter is **python3.4**, were all good to go.

Now, compile
```
make 
```
Should end up like with:
```
Linking CXX shared module ../../lib/python3/cv2.cpython-34m.so
[100%] Built target opencv_python3
(myvirtualenv)erik@ubuntutrusty-opencv:/mnt/opencv/release$ 
```

Now, install
```
make install
```
You should now have an installation in your "install path".

## Add openCV libs (cv2.cpython-34m.so) into your python virtual-env
Link the compiled opencv libraries to your virtualenv:
```
cd ~/virtualenvs/myvirtualenv/lib/python3.4
ln -s ~/opencv3.1.0/lib/python3.4/site-packages/cv2.cpython-34m.so
```

Validate by running:
```
python
import cv2
cv2.__version__
```

Your done!


