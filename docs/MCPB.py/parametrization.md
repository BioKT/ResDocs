# Parametrization setup

These brief instructions are a general guide to how to parametrize a simple 
metalloprotein system. More detailed tutorials for a variety of systems 
can be found [elsewhere](http://ambermd.org/tutorials/advanced/tutorial20/mcpbpy.htm).
In this case we are simulating a monomeric Amyloid &beta; - Zn(II) system with a 1:1 stoichiometry
using the AmberTools21 software version, but things should not change too much for other recent 
versions of the software. The parameters will be obtained for the Amber99SB*-ILDN force field.
You must have a working version installed in your machine.


### Get the files

In order to start with the parametrization, you must download the PDB file of the system. 
In this case, we will be working with the [PDB ID: 1ZE9 system](https://www.rcsb.org/structure/1ze9).
The file must be downloaded in the PDB format.

### Preparing the files

The first step is separating the different fragments present in the PDB file. One file must include
the metal exclusively and another file should contain the protein chain. An additional file should 
be created if a ligand is present although this is not the case. 

The metal must be separated to a file. The name of the file must be in capital letters, i.e., in this
case, since the metal being separated is Zn(II), the file must be named ZN.pdb. Then, the file
must be converted from PDB to MOL2 format:

```
metalpdb2mol2.py -i ZN.pdb -o ZN.mol2 -c 2
```
where, the charge must be specified with the -c flag.

Then, the file containing the protein must be processed. Both the ACE and NH2 residues
must be removed. Once the file is processed, protons must be added using Chimera.
It is important to know the protonation state of each histidine (if present). In this case,
both His6 and His14 are protonated in the &epsilon; N (HIE) and His13 is protonated in 
the &delta; N (HID). To protonate the protein:

- Open Chimera and load PDB file
- Tools > Structure Editing > AddH
- Consider H-bonds
- Protonation states for histidine > Residue-name-based

Once the protein is protonated, both protein and metal PDB files
must be merged:

```
cat 1ze9_chimera.pdb ZN.pdb > 1ze9_H.pdb
```
One must be cautious when merging PDB files, as the **END** line present in
the protein PDB file will be written before the PDB of the metal. Therefore, removing
the line is indispensable.

Once the files are merged, the newly merged PDB file is renumbered:

```
pdb4amber -i 1ze9_H.pdb -o 1ze9_renum_H.pdb
```

### Running the MCPB.py program

The first thing needed is an input file for the MCPB.py program. This file (1ze9.in), should
include the following information:

```
original_pdb 1ze9_renum_H.pdb
group_name 1ze9
cut_off 2.8
ion_ids 254
ion_mol2files ZN.mol2
large_opt 1
force_field ff99SB
software_version g09
```

The `cut_off` value specifies that a bond exists between the metal ion and 
surrounding atoms, therefore, it must be double checked to ensure the cutoff value is selected 
correctly. To double check, the `1ze9_small_opt.com` file should 
be visualized to ensure that the desired metal-protein bonds are present.
The ion_ids value specifies the atom number of the ion present in the
specified PDB file. The `large_opt` variable is used to indicate whether to do 
a geometry optimization in the Gaussian input file. With the value 1, we indicate that an 
optimization of the hydrogen positions will be done. More variables can be indicated
in the input file, such as different force fields, different softwares to run QM calculations
and many more. For more information see pages 288-290 in the 
[Amber 2016 reference manual](http://ambermd.org/doc12/Amber16.pdf).

Once the ipunt file is ready, the MPCB.py program is run:

```
MCPB.py -i 1ze9.in -s 1
```

This line will create three Gaussian input files:

```
1. 1ze9_small_opt.com
2. 1ze9_small_fc.com
3. 1ze9_large_mk.com
```

Each of the QM calculations must be done in the same order they are listed in.
The first calculation will optimize the structure to give equilibrium geometry constants.
Once the first calculation is done, the checkpoint file (.chk.gz) must be present in the
directory in which the second calculation will be made. The second calculation will obtain
force constants. In order to perform the third calculation, some processing of the second
calculation checkpoint must be made:

```
gzip -d 1ze9_small_opt.chk.gz
formchk 1ze9_small_opt.chk > 1ze9_small_opt.fchk
```

The processed checkpoint file must also be present in the directory where the third calculation
will be performed. The third calculation performs the Merz-Kollman RESP charge calculation to 
obtain the optimized charges of the system.

Next, the Seminario method will be used to generate force field parameters:

```
MCPB.py -i 1ze9.in -s 2
```

This line will create the `1ze9_mcpbpy.frcmod` file which will be used
in the leap modelling.

```
MCPB.py -i 1ze9.in -s 3
```

The RESP charge fitting is performed and the mol2 files for the metal site residues are obtained:

- ZN1.mol2
- HE1.mol2
- GU1.mol2
- HD1.mol2
- HE2.mol2

```
MCPB.py -i 1ze9.in -s 4
```

The tleap input file is generated (`1ze9_tleap.in`) and a new PDB file is created,
where the residues coordinating the metal ion are renamed (`1ze9_mcpbpy.pdb`).
In the new PDB file, ASP residues are to be renamed as ASH for tleap to process them. Occasionally, 
an incorrect bond could be created in the tleap input file. Bonds are defined as:

```
bond mol.X.atm2 mol.Y.atm2
```

The X and Y define the residue number, whereas atm1 and atm2 define the atom name present in each residue.
Each line will define a bond in such way. Generally, bonds between metal-protein as well 
as "links" between renamed residues and the rest of the protein chain should be present.

Once the files are corrected, tleap is used to generate the topology and coordinate files:

```
tleap -s -f 1ze9_tleap.in > 1ze9_tleap.out
```

Tleap should have created a topology (`1ze9_dry.prmtop`) and
a coordinate file (`1ze9_dry.inpcrd`).

Lastly, parameters are converted from Amber to Gromacs synthax using acpype:

```
acpype -p 1ze9_dry.prmtop -x 1ze9_dry.inpcrd -o gmx -n -1 -l
```

A new directory called `1ze9_dry.amb2gmx` is created where all Gromacs
parameters are stored.
