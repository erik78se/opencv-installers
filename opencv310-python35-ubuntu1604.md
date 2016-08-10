# Install python3.5 + openCV 3.1.0 + ubuntu 16.04

Based on this tutorial by : http://www.hiroom2.com/2016/05/20/ubuntu-16-04-install-opencv-3-1/

This guide sets up:
* openCV 3.1.0
* opencv_contrib 3.1.0
* ... and gets it to work in a virtualenv.

Make sure to have about 20GB of disk space, since the build and installation takes alot of room. Consider this if you intend to perform this on a virtual computer with limited space available.

## Your OS requirements
* Ubuntu 16.04

## Install some important dependencies for openCV
```
sudo apt-get build-dep -y opencv
```
At this point, python3.5 should already be available, if not -> Install it.

## Build a virtual env - the python3 way
First, install python3-venv
```
sudo apt-get install python3-venv
```
Now, create a virtual env (as your own user, don't be root)
```
python3.5 -m venv python35-opencv
```

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
--     Interpreter:                 /home/erik/myvirtualenv/bin/python3.4 (ver 3.4.4)
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


