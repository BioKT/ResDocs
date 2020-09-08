# Gromacs benchmarks in our systems 

## Hydrogenase 
This system consists of a protein with 19054 water molecules
plus ions, summing a total of 83764 atoms. We have run 
simulations in both the ATLAS and ARINA clusters.
Below we show PBS scripts for both supercomputers.

### ATLAS

```
#!/bin/bash
#PBS -q qchem
#PBS -l nodes=1:ppn=16
#PBS -l mem=1gb
#PBS -l cput=1000:00:00
#PBS -N qchem

module load GROMACS/2019.4-foss-2019b

cd $PBS_O_WORKDIR

export NPROCS=`wc -l < $PBS_NODEFILE`

tpr="dd_ff03w_tip4p2005_ions0.1_posre.tpr"

np=8
ntomp=2

out="ntmpi${np}_ntomp${ntomp}"
gmx mdrun -ntmpi $np -ntomp $ntomp -s $tpr -deffnm $out
```

### KALK
```
#!/bin/bash
#PBS -l nodes=nd15:ppn=16
#PBS -l walltime=480:00:00
#PBS -joe
#PBS -q gpu2
## Do not change below this line
. /home/users/etc/send_funcs.sh
HOST=$(hostname)
echo Job runing on $HOST
echo $PBS_O_WORKDIR > /home/qsub_priv/pbs_paths/PBS.$PBS_JOBID.$USER
chmod 755  /home/qsub_priv/pbs_paths/PBS.$PBS_JOBID.$USER
cd $PBS_O_WORKDIR
chooseScr
cd $scr
#echo "cp -r $PBS_O_WORKDIR/* $scr/."
cp -r  $PBS_O_WORKDIR/*tpr $scr/.
#source /software/gromacs/bin/GMXRC.bash

#module load GROMACS/2019.4-fosscuda-2018b
module load GROMACS/2020-fosscuda-2018b

tpr=dd_ff03w_tip4p2005_ions0.1_posre
out=$tpr
/usr/bin/time -p mpirun -np 1 gmx_mpi mdrun -ntomp 16 -quiet -nb gpu -gpu_id 01 -s $tpr -deffnm $out > $PBS_JOBID.out
recover
rm -f  /home/qsub_priv/pbs_paths/PBS.$PBS_JOBID.$USER
```

