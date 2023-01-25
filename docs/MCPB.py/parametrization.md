# Parametrization setup

These brief instructions are a general guide to how to parametrize a simple 
metalloprotein system. More detailed tutorials for a variety of systems 
can be found [elsewhere](http://ambermd.org/tutorials/advanced/tutorial20/mcpbpy.htm).
In this case we are simulating a monomeric Amyloid $\beta$ - Zn(II) system with a 1:1 stoichiometry
using the AmberTools21 software version, but things should not change too much for other recent 
versions of the software.
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
both His6 and His14 are protonated in the $\varepsilon$ N (HIE) and His13 is protonated in 
the $\delta$ N (HID). To protonate the protein:

- Open Chimera and load PDB file
- Tools > Structure Editing > AddH
- Consider H-bonds
- Protonation states for histidine > Residue-name-based

Once the protein is protonated, both protein and metal PDB files
must be merged:

```
cat 1ze9_chimera.pdb ZN.pdb > 1ze9_H.pdb
```
One must be cautious when merging PDB files, as the $\textit{END}$ line present in
the protein PDB file will be written before the PDB of the metal. Therefore, removing
the line is indispensable.

Once the files are merged, the newly merged PDB file is renumbered:

```
pdb4amber -i 1ze9_H.pdb -o 1ze9_renum_H.pdb
```

### Running the MCPB.py program
