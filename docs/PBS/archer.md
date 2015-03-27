This is just an example PBS script for submitting jobs in the 
[Archer](https://www.archer.ac.uk)
supercomputing facility.

´´´
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
´´´
