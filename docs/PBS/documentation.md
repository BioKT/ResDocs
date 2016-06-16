# Installing Torque
Bellow follow instructions on how to install [Torque](http://www.adaptivecomputing.com/products/open-source/torque/)
in a multiprocessor Ubuntu Linux server. In this case the same machine is used as server, 
scheduler, submission node and compute node. These notes have been borrowed from 
[this blog post](https://jabriffa.wordpress.com/2015/02/11/installing-torquepbs-job-scheduler-on-ubuntu-14-04-lts/) 
(thanks!) and are kept here for future records only. The version of Ubuntu used in this 
case was 14.04 LTS. 

The first thing to note is that you should do all of these as `root`. Then we must ensure 
that the first line in the `/etc/hosts` file reads as follows

```
127.0.0.1	localhost
```

Next comes the installation of some packages, which we do using Ubuntu`s package manager.

```
apt-get install torque-server torque-client torque-mom torque-pam
```

# Example PBS script

This is just an example PBS script for submitting jobs in the 
[Archer](https://www.archer.ac.uk)
supercomputing facility.

```
#!/bin/bash --login
#PBS -N jobname

# Select 1  node
#PBS -l select=1

#PBS -l walltime=24:00:00
#PBS -m abe
#PBS -M name@emailprovider.org

# Replace this with your budget code
#PBS -A budget

# Move to directory that script was submitted from
#export PBS_O_WORKDIR=$(readlink -f $PBS_O_WORKDIR)
#echo $PBS_O_WORKDIR
#exit
#cd $PBS_O_WORKDIR
cd "/work/directory/"

# Load the GROMACS module
module add gromacs

# Run GROMACS using default input and output file names
pull=1
k=100

options="-s ${file} -deffnm ${file}"

aprun -n 24 mdrun_mpi $options
```
