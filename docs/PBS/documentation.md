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

After this step we simply stop these services since, apparently, the initial torque
 configuration does not really work as one would hope. In order to achieve this we simply
type the following in the terminal
```
/etc/init.d/torque-mom stop
/etc/init.d/torque-scheduler stop
/etc/init.d/torque-server stop
```

We then can create a new setup for torque using the following
```
pbs_server -t create
```
When prompted about whether we want to overwrite the existing database we will reply yes 
(`[y]`). Next the just-started server instance is killed using the following command for
further configuration
```
killall pbs_server
```
Next we will set up the server process. In my case the server is simply called `localhost`
and I experienced some problems when trying to use a different server domain. 
```
echo localhost > /etc/torque/server_name
echo localhost > /var/spool/torque/server_priv/acl_svr/acl_hosts
echo root@localhost > /var/spool/torque/server_priv/acl_svr/operators
echo root@localhost > /var/spool/torque/server_priv/acl_svr/managers
```
The following step is to simply add the compute nodes. Since here we are using the 
"head node" as "compute node" then we just need to type the following
```
echo "localhost np=56" > /var/spool/torque/server_priv/nodes
```

Then we start the MOM process that handles the compute node
```
echo localhost > /var/spool/torque/mom_priv/config
```

After all of these one has to restart the processes again
```
/etc/init.d/torque-server start
/etc/init.d/torque-scheduler start
/etc/init.d/torque-mom start
```

Finally we need to restart the scheduler, create the default queue and
configure thee server to allow submissions from itself
```
qmgr -c "set server scheduling = true"
qmgr -c "set server keep_completed = 300"
qmgr -c "set server mom_job_sync = true"
# create default queue
qmgr -c "create queue batch"
qmgr -c "set queue batch queue_type = execution"
qmgr -c "set queue batch started = true"
qmgr -c "set queue batch enabled = true"
qmgr -c "set queue batch resources_default.walltime = 1:00:00"
qmgr -c "set queue batch resources_default.nodes = 1"
qmgr -c "set server default_queue = batch"
# configure submission pool
qmgr -c "set server submit_hosts = localhost"
qmgr -c "set server allow_node_submit = true"
```

Finally you can test whether everything is working right for you using the following command
```
qsub -I
```
An additional test script that can be done is to run this simples PBS script `test.sh`
```
#!/bin/bash
cd $PBS_O_WORKDIR
#direct the output to cluster_nodes
cat $PBS_NODEFILE > ./cluster_nodes
```
This should run by simply writing the following command on your terminal 
```
qsub test.sh
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
