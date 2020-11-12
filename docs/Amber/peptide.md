# Building molecules with tleap 
In order to build PDB structures from sequence
one may use programs like LEaP, included with
Ambertools. The example below corresponds to 
the specific case of an acetylated and amidated 
alanine pentapeptide.

We start by starting LEaP, which in this
specific case was installed through ananconda

```
$> tleap
-I: Adding /Users/.../anaconda3/dat/leap/prep to search path.
-I: Adding /Users/.../anaconda3/dat/leap/lib to search path.
-I: Adding /Users/.../anaconda3/dat/leap/parm to search path.
-I: Adding /Users/.../anaconda3/dat/leap/cmd to search path.

Welcome to LEaP!
(no leaprc in search path)
```

This will leave a prompt for you to type a series
of commands. Next we define the sequence of the peptide

```
> source leaprc.protein.ff14SBonlysc
> ala5 = sequence {ACE ALA ALA ALA ALA ALA NHE}

----- Source: /Users/.../anaconda3/dat/leap/cmd/leaprc.protein.ff14SBonlysc
----- Source of /Users/.../anaconda3/dat/leap/cmd/leaprc.protein.ff14SBonlysc done
Log file: ./leap.log
Loading parameters: /Users/.../anaconda3/dat/leap/parm/parm10.dat
Reading title:
PARM99 + frcmod.ff99SB + frcmod.parmbsc0 + OL3 for RNA
Loading parameters: /Users/.../anaconda3/dat/leap/parm/frcmod.ff14SB
Reading force field modification type file (frcmod)
Reading title:
ff14SB protein backbone and sidechain parameters
Loading parameters: /Users/.../anaconda3/dat/leap/parm/frcmod.ff99SB14
Reading force field modification type file (frcmod)
Reading title:
ff99SB backbone parameters (Hornak & Simmerling) with ff14SB atom types
Loading library: /Users/.../anaconda3/dat/leap/lib/amino12.lib
Loading library: /Users/.../anaconda3/dat/leap/lib/aminoct12.lib
Loading library: /Users/.../anaconda3/dat/leap/lib/aminont12.lib
```

Finally, we can save the structure to a PDB file
```
> savepdb ala5 ala5.pdb
Writing pdb file: ala5.pdb
```
