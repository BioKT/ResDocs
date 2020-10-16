# Submission scripts for our local supercomputers
We are using both the Arina and Atlas clusters for
running jobs using the Gromacs software package.
Below we show PBS scripts for both supercomputers.

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

tpr="topol.tpr"
/usr/bin/time -p mpirun -np 1 gmx_mpi mdrun -ntomp 16 -quiet -nb gpu -gpu_id 01 -s $tpr > $PBS_JOBID.out
recover
rm -f  /home/qsub_priv/pbs_paths/PBS.$PBS_JOBID.$USER
```

#### ATLAS-FDR

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

tpr="topol.tpr"

np=8
ntomp=2

gmx mdrun -ntmpi $np -ntomp $ntomp -s $tpr
```

### ATLAS-EDR
We are also using the extension of the Atlas supercomputer, which 
uses SLURM instead of PBS. A submission script for Gromacs looks as 
follows. You can check the documentation for options in Slurm 
[here](http://dipc.ehu.es/cc/computing_resources/jobs/batch_systems/slurm).

```
#!/bin/bash
#SBATCH --partition=long
#SBATCH --job-name=hase
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=4
#SBATCH --cpus-per-task=1
#SBATCH --gres=gpu:p40:1
#SBATCH --time=1-00:00:00
#SBATCH --mem=64G

module load GROMACS/2020-fosscuda-2019b

tpr="topol.tpr"

np=8
ntomp=1
ngpu=2

mpirun --map-by ppr:4:node -np $np gmx_mpi mdrun -ntomp $ntomp -s $tpr
```
