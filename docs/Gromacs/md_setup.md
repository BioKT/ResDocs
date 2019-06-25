# Running a simulation in Gromacs

These brief instructions are a general guide to how to run a simple MD simulation using 
Gromacs. More detailed tutorials for a variety of systems can be found 
[elsewhere](http://www.mdtutorials.com/gmx/index.html).
In this case we are simulating the alanine dipeptide using Gromacs 2018 but 
things should not change too much for other recent versions of the software.
You must have a working version installed in your machine. 

### Get the files
First of all, you will have to 
download the compressed files in
[alaTB_files.tar.gz](https://drive.google.com/file/d/1V6fZSjJbAAqDeQY_J5BHu0GQuj6eMtNx/view?usp=sharing)
to your computer and extract them typing 
```
tar -xvf ala_TB_files.tar.gz
```
in your terminal. When you do this, you should be able to find a file called `alaTB.gro` together with
 a number of `mdp` files that we will later use.

### Generate the topology
The next step is generating a topology file from the configuration file `alaTB.gro`.
We will do this using the ```pdb2gmx``` program. Before we do this we will generate variables in
bash with informative names which will be useful for sensible file name structure.
This is what we will run in our terminal

```
proot="alaTB"
ff="ff03"
wat="tip3p"

out="${proot}_${ff}_${wat}"
top="${proot}_${ff}_${wat}"
gmx pdb2gmx -f $proot -o $out -p $out <<EOF
1
1
EOF
```
Using `1` and `1` as options after the `EOF` we are choosing specific options for 
our force field and water model
(Amber03 and TIP3P respectively). These will suffice in this illustrative example but it 
would be advisable to do some research on your system for an educated choice of force 
field and water model combination.

### Editing the box and solvation
Next we will change the size of our simulation box and we will fill it with water molecules.
For this we will use two additional Gromacs programs: 
```
inp=$out
out="${proot}_${ff}_${wat}_edit"
gmx editconf -f $inp -o $out -c -d 1 -bt cubic

inp=$out
out="${proot}_${ff}_${wat}_solv"
gmx solvate -p $top -cp $inp -o $out

```
The first command edits the box so that the molecule is centered (`-c`) in the cubic (`-bt cubic`)
simulation box and it has 1 nm between molecule and box sides (`-d 1`). The second command
simply solvates the molecule filling the box with the water model geometry of choice.

### Energy minimization
Considering that we are starting from a peptide conformation and a fixed water box configuration
 that are not particularly meaningful, we will first energy minimize the system using steepest 
descent.
```
mdp="mini.mdp"
inp="${proot}_${ff}_${wat}_solv"
out="${proot}_${ff}_${wat}_mini"
gmx grompp -p $top -c $inp -f $mdp -o $out

inp=$out
out=$inp
gmx mdrun -v -s $inp -deffnm $out
```
As we will do many times later, we first use the Gromacs preprocessor (`grompp`) and then run the
calculation (`mdrun`). Since we are using the verbose (`-v`) option you will find that this produces
a lot of output, corresponding to the gradual decrease in the energy of the system.

```
mdp="nvt_posre.mdp"
inp=$out
out="${proot}_${ff}_${wat}_nvt_posre"
#gmx grompp -f $mdp -c $inp -p $top -r $inp -o $out

```
```
inp=$out
out=$inp
gmx mdrun -v -s $inp -deffnm $out

```
```
mdp="npt.mdp"
inp=$out
out="${proot}_${ff}_${wat}_npt"
gmx grompp -f $mdp -c $inp -p $top -r $inp -o $out -t ${inp}.cpt

```
```
inp=$out
out=$inp
gmx mdrun -v -s $inp -deffnm $out

```
```
mdp="sd_nvt.mdp"
inp=$out
out="${proot}_${ff}_${wat}_nvt"
gmx grompp -f $mdp -c $inp -p $top -r $inp -o $out -t ${inp}.cpt

```
```
inp=$out
out=$inp
gmx mdrun -v -s $inp -deffnm $out
```
