## Gromacs installation in Mac OS X

(These instructions were borrowed from 
[Phillip W Fowler´s](http://philipwfowler.me/) blog).

Prerequisites for the installation are the gcc compilers available at MacPorts.
This requires you to first install Xcode.

Then the most important thing is to download the source code from the Gromacs 
website and unpack the software.

```
tar xvf gromacs-5.0.4.tar.gz
cd gromacs-5.0.4
mkdir build
```

Then we start with the interestign stuff. In the last few versions Gromacs
has made a transition towards using cmake instead of make. Cmake is readily 
available for Mac OS X, so no problem with this. Then you must run the following
in the command line:

```
cmake .. -DGMX_BUILD_OWN_FFTW=ON -DCMAKE_INSTALL_PREFIX='/usr/local/gromacs/5.0.4/'
make
sudo make install
```

This will install the program in `/usr/local/gromacs/5.0.4`, and editing your `.bashrc`
file you will be able to choose the version of Gromacs that is running.


Adding MPI support on a Mac is trickier. This appears mainly to be because the gcc 
compilers from MacPorts  do not appear to support OpenMPI. Here is a workaround 

```
sudo port install openmpi
```

Now we need a compiler that supports OpenMPI

```
sudo port install openmpi-devel-gcc49
```

Finally, we can follow the steps above but now we need a more complex cmake instruction

```
cmake .. -DGMX_BUILD_OWN_FFTW=ON
	-DGMX_BUILD_MDRUN_ONLY=on
	-DCMAKE_INSTALL_PREFIX=/usr/local/gromacs/5.0.2
	-DGMX_MPI=ON -DCMAKE_C_COMPILER=mpicc-openmpi-devel-gcc49
	-DCMAKE_CXX_COMPILER=mpicxx-openmpi-devel-gcc49
	-DGMX_SIMD=SSE4.1
```

This is only going to build an MPI version of `mdrun`, as the other Gromacs programs do not
run in parallel. We have to tell cmake what all the new fancy compilers are called and, 
unfortunately, these don’t support AVX SIMD instructions so we have to fall back to SSE4.1. 
Experience suggests this doesn’t impact performance as much as you might think. 
