# Beast and Beagle installation in a Linux server

This instructions are valid for a Linux server running Ubuntu 14.04 LTS, with
2 Intel Xeon processors and 2 Nvidia GPUs. These instructions are based on
those found in the [Beast](http://beast.bio.ed.ac.uk/BEAGLE) and 
[Beagle](https://github.com/beagle-dev/beagle-lib/wiki/LinuxInstallInstructions) 
sites, plus my own experience.

The first thing I had to do was to get all the dependencies right, for which I run
the following
```
sudo apt-get install build-essential autoconf automake libtool subversion pkg-config openjdk-6-jdk
```

Once these were all in place, I tried using the NVIDIA OpenCL implementation 
which was accessible via the apt-get command. However, I was unable to install
the Beagle library so that it was recognized. Tests systematically failed. 
Hence, I resorted to the Intel implementation of 
[OpenCL](curl http://registrationcenter-download.intel.com/akdlm/irc_nas/9019/opencl_runtime_16.1_x64_ubuntu_5.2.0.10002.tgz -o opencl_runtime_16.1_x64_ubuntu_5.2.0.10002.tgz).  After the download, this can be easily installed 

```
cd opencl_runtime_16.1.1_x64_ubuntu_6.4.0.25/
sudo bash install.sh
```

Then I went back to the installation of the Beagle library.

```
./autogen.sh
./configure --prefix="/opt/beast/beagle-lib" --with-opencl="/opt/intel/opencl/lib64"
sudo make install
```

In order to check whether I was getting the correct functionality I run the 
test suite 

```
make check
```

Finally all went well. So that allowed running the downloaded Beast executable available 
[here](https://github.com/beast-dev/beast-mcmc/releases/download/v1.8.3/BEASTv1.8.3.tgz).

