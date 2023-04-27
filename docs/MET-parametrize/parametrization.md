# Parametrizing an amino acid using AmberTools21
Below is a step-by-step guide for amino acid parametrization
using the tools provided with the 
[Amber MD package](https://ambermd.org/). Specifically, we
will be using [Ambertools](https://ambermd.org/AmberTools.php),
which can be installed using Anaconda 

   conda install -c conda-forge ambertools compilers

### Build a structure

In order to paramterize an amino acid, the first thing we need
is an structure. Conventionally, the structure used to parametrize
an amino acid is to build an structure with the amino acid with
ACE and NME caps, e.g., if I want to parametrize a methionine my
structure will be ACE-MET-NME. To do so, we need to build the amino
acid using the building software of our liking (Amber's tleap, 
Gaussview or any other works). The structure built for this tutorial
can be found here: [MET.xyz](https://drive.google.com/file/d/14VTesUlBdBR3O2gln5xxqVhOX5dlqAq7/view?usp=sharing).
Mind that atom numbers are ordered to match the sequence (ACE-MET-NME), and
that in the case of the aminoacid, the atom number of the atoms of MET
match the order in which they appear in GROMACS. The order of the atoms
will be important for future steps.

### Quantum-mechanical calculations

Once the structure is built, one must follow the steps mentioned in the
artcile of the force field selected. To easily find which paper one should
follow, one can directly find it referenced in the `forcefield.doc` file
present in each force field directory present in the `${gromacs-path}/share/
gromacs/top/${force-field}.ff` directory. Since in this tutorial we are
interested in obtaining parameters for the Amber99SB*-ILDN force field,
[this](https://onlinelibrary.wiley.com/doi/full/10.1002/1096-987X%28200009%2921%3A12%3C1049%3A%3AAID-JCC3%3E3.0.CO%3B2-F)
is the article we should be looking at. In this article, it is clearly 
stated that RESP charges were calculated following these steps: 
>"Quantum-mechanical optimizations were performed for all the compounds using the 6-31G* basis set (...) electrostatic potentials were then calculatedat the same level for the minimized geometry."

To perform the optimization of the structure, we will be using the 
Gaussian16 software that is available both in Arina and Atlas. 
The input file can be found
here: [MET.com](https://drive.google.com/file/d/1uwgWsbBYX_GkuoYXiIxZOoyUzQeSApi5/view?usp=sharing).
All output files are included in this directory: [MET-optimization](https://drive.google.com/drive/folders/1KP_juucM5HoVWYu1N6cnb1D87KyUdrr3?usp=sharing).

Once the geometry optimization is done, a second calculations to calculate
charges is done using this input file: [MET-charges.com](https://drive.google.com/file/d/1OtG-hHixxj28nn3dZAFq_VbB3i3yNOQJ/view?usp=sharing).
This calculation must be performed in the same directory as the `00.chk.gz`
file as the geometry will be read from it. With the `Integral` keyword we
modify the method of computation and use two-electron integrals and their
derivatives. With the `Population` keyword we print molecular orbitals
and several types of population analysis and atomic charge assignements.
Lastly, with the `IOp` keyword we set internal options. All the
configurations for this last part have been taken from the workflow created
with the MCPB.py program of AmberTools. Files of the charge calculations
can be found in this directory: [MET-charges](https://drive.google.com/drive/folders/1fBlb3yddsKRmj5ysZJMOxwwqVAsf7Kdv?usp=sharing).

### Parametrizing with AmberTools

All files necessary for this following part are available here:
[Amber-files](https://drive.google.com/drive/folders/1CScwN_MEvd2U18LFbZkF3OLA9JXDM8AF?usp=sharing).

The first step is to perform the RESP calculation. The electrostatic 
potential has to be reformated to obtain a RESP-friendly syntax. We will
achieve so by processing the Gaussian output with `espgen`:

```
espgen -i MET-charges.log -o esp.dat
```

Then, two additional files must be created: `resp.in` and `resp.qin`.
The `resp.in` file indicates a variety of options for the RESP calculation:

```
capped-resp run 1	#This is name of the calculation, it is up to you
 &cntrl
 nmol=1,		#Number of molecules included in the calculation
 ihfree=1,		#Weak restraints only in the heavy atoms
 qwt=0.0005,		#This is the strength of the restraint in A.U
 iqopt=2,		#Read the initial charges from the resp.qin file
 /
    1			#Number of the molecule included
MET			#Name of the residue, this info is just for you
    0   29		#Charge of the molecule and number of amtoms
```

The following lines indicate the atomic number of each atom. If a -1 is 
indicated after the atomic number, the charge must be specified in the
`resp.qin` file, whereas if a 0 is indicated, the charge is calculated.
Additionally, if another number appears, both that atom and the atom 
indicated will have the same charge (e.g., H12 and H13 or H15 and H16).

In the `resp.qin` file the charges to the ACE and NME are given. 
Additionally, the charges of the NH- and CO- are indicated, as for the
Amber99SB*-ILDN force field these charges are the same for all amino acids.

Once these files are ready, we run the RESP calculation:

```
resp -O -i resp.in -o resp.out -p resp.pch -t resp.chg -q resp.qin -e esp.dat
```

If having errors such as "At line 403 of file resp.F (unit=5,file='resp.in')"
error, you have to look at the spaces present in your `resp.in` file. Mind
that a blank line must be added at the end of the `resp.in` file.

Next, we run antechamber, which will associate the partial charges to each
atom:

```
antechamber -fi gout -i MET-charges.log -bk MET -fo ac -o MET.ac -c rc -cf resp.chg -at amber
```

Now, we will delete the capping groups from the custom residue. To do so
we need the `MET.mc` file, which is pretty self explanatory. The omited
atoms are the ones belonging to the caps. One must then run the following
line:

```
prepgen -i MET.ac -o MET.prepin -m MET.mc -rn MET
```

Lastly, we will generate the parameters using parmchk2:

```
parmchk2 -i MET.ac -f ac -o MET_gaff.frcmod
```

The final step is then to run tleap to obtain the Amber parameters. Open
tleap by typing: tleap.

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
