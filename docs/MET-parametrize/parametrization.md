# Parametrizing an amino acid using AmberTools21
Below is a step-by-step guide for amino acid parametrization
using the tools provided with the 
[**Amber MD package**](https://ambermd.org/). Specifically, we
will be using [**Ambertools**](https://ambermd.org/AmberTools.php),
which can be installed using Anaconda simply typing
```
conda install -c conda-forge ambertools compilers
```

### Build a structure
In order to parametrize an amino acid, the first thing we will 
need is a structure, which we will normally build in the 
acetylated and amidated form (i.e. with ACE and NME caps). 
Here we illustrata parametrization with the methionine residue,
and hence we will need a structure for ACE-MET-NME (we can generate
it using [**tleap**](Amber/peptide.md) or **Gaussview**). 

In this example, we have created the structure for you. It can be 
downloaded from the following [link](https://drive.google.com/file/d/14VTesUlBdBR3O2gln5xxqVhOX5dlqAq7/view?usp=sharing).
Mind that atom numbers are ordered to match the sequence (ACE-MET-NME), and
that in the case of the aminoacid, the atom number of the atoms of MET
match the order in which they appear in **GROMACS**. The order of the atoms
will be important for future steps.


### Choose your force field 
Once the structure is built, one must follow the steps mentioned in the
relevant article of the force field of choice. Here, we will generate 
parameters for the 
Amber99SB\*-ILDN force field, which includes corrections in both 
the [backbone](https://doi.org/10.1021/jp901540t) and 
[sidechains](https://doi.org/10.1002/prot.22711) torsions. 


### Quantum-mechanical calculations
To perform the optimization of the structure, we will be using the 
**Gaussian16** software that is available both in the Arina and Atlas
clusters. The input file for geometry optimization can be found
 [here](https://drive.google.com/file/d/1uwgWsbBYX_GkuoYXiIxZOoyUzQeSApi5/view?usp=sharing).
The file looks something like this
```
%chk=00.chk
# B3LYP/6-31G* Opt

Methionine

0  1
 C     2.000000     2.090000     0.000000
 H     1.486000     2.454000     0.890000
 H     1.486000     2.454000    -0.890000
...
```
where we have ommited most of the coordinates in the input. 
For convenience, we have prepared a folder including all the 
[output files](https://drive.google.com/drive/folders/1KP_juucM5HoVWYu1N6cnb1D87KyUdrr3?usp=sharing).

Once the geometry optimization is done, a second calculation must be performed
 to derive the charges. For this, you will need the following [input 
file](https://drive.google.com/file/d/1OtG-hHixxj28nn3dZAFq_VbB3i3yNOQJ/view?usp=sharing).
The contents of the file are as follows:
```
%chk=00.chk
# B3LYP/6-31G* geom=check guess=read Integral=(Grid=UltraFine) Pop(MK,ReadRadii)
IOp(6/33=2,6/42=6)

Methionine

0  1
```
With the `Integral` keyword we modify the method of computation and use 
two-electron integrals and their derivatives. 
With the `Population` keyword we print molecular orbitals
and several types of population analysis and atomic charge assignements.
Lastly, with the `IOp` keyword we set internal options. 
This calculation must be run in the same directory as the `00.chk.gz`
file as the geometry will be read from it.
All the configurations for this last part have been taken from the workflow
 created with the **MCPB.py** program of **AmberTools**. 
The output from this 
calculation can be downloaded from [here](https://drive.google.com/drive/folders/1fBlb3yddsKRmj5ysZJMOxwwqVAsf7Kdv?usp=sharing).

### Parametrizing with AmberTools
Next we jump into the actual parametrization of the molecular mechanics
force field. All the files necessary for this part are available in this
[folder](
https://drive.google.com/drive/folders/1CScwN_MEvd2U18LFbZkF3OLA9JXDM8AF?usp=sharing).

The first step is to perform the fitting of the charges using 
the Restrained Electrostatic Potential (RESP) method. The electrostatic 
potential has to be reformated to obtain a RESP-friendly syntax. We will
achieve so by processing the Gaussian output with the **espgen**
program:
```
espgen -i MET-charges.log -o esp.dat
```

Then, two additional files must be created: `resp.in` and `resp.qin`.
The `resp.in` file indicates a variety of options for the RESP calculation.
It looks as follows:
```
capped-resp run 1	#This is name of the calculation, it is up to you
 &cntrl
 nmol=1,     #  Number of molecules included in the calculation
 ihfree=1,   #  Weak restraints only in the heavy atoms
 qwt=0.0005, #  This is the strength of the restraint in A.U
 iqopt=2,    #  Read the initial charges from the resp.qin file
 /
    1        #  Number of the molecule included
  MET        #  Name of the residue, this info is just for you
0  29        #  Charge of the molecule and number of atoms
...
```
This file header is followed by two additional columns of numbers. 
The first column indicates the atomic number of each atom. The value 
in the second column determines whether the charge must be calculated
 (if the value is 0) or, alternatively, the charge is defined by the user
 (if the value -1). 
In the latter case, the charge will be set in the `resp.qin` file.
In the current parametrization of a Met residue, the lines corresponding
 to ACE and NME atoms have a value of -1; hence their charges are set 
by the user. We can find them in the `resp.qin` file. 
The charges of the NH- and CO- are also indicated, as for the
Amber99SB\*-ILDN force field these charges are the same for all amino acids.
Finally, if a number other than 0 or -1 appears in the second column for
a given atom, it will be constrained to have the same charge as that
corresponding to the atom index (e.g., H12 and H13 or H15 and H16
in our example).

Once these files are ready, we run the RESP calculation:
```
resp -O -i resp.in -o resp.out -p resp.pch -t resp.chg -q resp.qin -e esp.dat
```
If you get error messages such as 
`At line 403 of file resp.F (unit=5,file='resp.in')`
error, you have to look at the spaces present in your `resp.in` file. Mind
that a blank line must be added at the end of the `resp.in` file.

The next step in our workflow is to run the **antechamber**  program, 
which will associate the partial charges to each atom:
```
antechamber -fi gout -i MET-charges.log -bk MET -fo ac -o MET.ac -c rc -cf resp.chg -at amber
```

Now, we will delete the capping groups from the custom residue.
To do so we need the `MET.mc` file, which is pretty self explanatory. The omited atoms are the ones belonging to the caps. 
One must then run the following line:
```
prepgen -i MET.ac -o MET.prepin -m MET.mc -rn MET
```

Lastly, we will generate the parameters using **parmchk2**:
```
parmchk2 -i MET.ac -f ac -o MET_gaff.frcmod
```

The final step is then to run **tleap** to obtain the Amber
 parameters. Open **tleap** by typing `tleap` in your terminal.
Once inside, type the following lines.

```
loadamberprep MET.prepin
source oldff/leaprc.ff99
loadamberparams MET_gaff.frcmod
aa = sequence {ACE MET NME}
saveAmberParm aa MET.prmtop MET.inpcrd
quit
```

To convert the parameters from Amber to GROMACS we then run acpype:

```
acpype -p MET.prmtop -x MET.inpcrd -o gmx -n -1 -l
```

Parameters with the GROMACS syntax will be located to a directory
called `MET.amb2gmx`. GROMACS parameters are available in this folder:
[GROMACS-files](https://drive.google.com/drive/folders/1c6v6RRbjuviPSuCLcg-vlnU8uC0RR6Ya?usp=sharing).
