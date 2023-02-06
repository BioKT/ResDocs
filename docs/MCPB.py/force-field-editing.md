# Including parameters to a force field

Since we will be adding the newly obtained parameters to the Amber99SB*-ILDN
force field, the first thing we must do is to retrieve the force field directory,
which can be found [here](https://www.gromacs.org/user_contributions.html). The file
to be downloaded is `ff99sb-star-ildn.tgz`. Once downloaded, to decompress
the file, simply run:

```
tar -xvf ff99sb-star-ildn.tgz
```

to obtain a directory called `ff99sb-star-ildn.ff`.

This force field directory should then be stored either in 
`gromacs/share/gromacs/top/` or in the working directory. A personal advice 
would be to store it in a separate directory where it is clearly stated that the 
edited version of the force field is stored, e.g., `gromacs-1ZE9/share/gromacs/top/`. 
Once the directory is placed in the desired location, you are ready to start adding parameters.


### Checking for equivalent residues

One importart step, is to check wheter the newly parametrized residues are chemicaly aquivalent
in any way. For example, when working with exact copies of the same protein in a dimer, at least
two residues will be equivalent. In those cases, all parameters of equivalent residues should be
averaged. To see some examples of dimer systems where averaged parameters are added, look at systems
5LFY, 2LI9 or 2MGT in the following [OSF repository](https://osf.io/y4zk5/).

The first step is to compare nonbonded parameters &sigma; and &epsilon; of the newly 
parametrized residue and the residue present in the original force field. To do so, you should
look at the `ff99sb-star-ildn.ff/ffnonbonded.itp` file and the `1ze9_dry.amb2gmx/1ze9_dry_GMX.top` 
file. If the parameters are the same, which is usually the case, one can ommit creating new atomtypes
for each atom of the residue. Otherwise, new atomtypes for each of the atoms should be created.

The parameters to be added in the `ff99sb-star-ildn.ff/` directory are the following:

1. In the `aminoacids.rtp` file a new residue with a new name should be created for each
residue that you have parametrized. In this file, charges of each atom are to be specified. 
A new atomtype should be created for the atom(s) bonded to the metal. For example, in the case of His13,
this being a HID residue and knowing that Zn(II) is coordinated via the NE2 atom, the new residue with 
the new atomtype should be added as:

        [ HID ]
        [ atoms ]
          ...
          NE2    NB    -0.57270
          ... 

    would be converted to

        [ HDB ]
        [ atoms ]
          ...
          NE2   NB3    -0.22712
          ... 

    The metal ion should be added following the same steps.

2. In the `aminoacids.hdb` and `aminoacids.vsd` files, you should copy and paste the same lines present for each 
residue and edit the residue name to match the newly created one.

3. In the `ffnonbonded.itp` file, nonbonded parameters &sigma; and &epsilon; must be added for every new
atomtype created.

4. In the `ffbonded.itp` file, bonded parameters for `bonds`, `angles` and `dihedrals` should be added. These paramters
are to be found in the `1ze9_dry.amb2gmx/1ze9_dry_GMX.top` file. The parameters to be added should include all parameters
where the metal ion is present as well as the new atomtypes. That means that, for the previous example, all `bonds`, `angles` 
and `dihedrals` in which NE2 participates should also be redefined for the new atomtype (NB3).

    The next files are to be found in the `gromacs-1ZE9/share/gromacs/top/` directory:

5. In the `residuetypes.dat` file a new line for each new residue should be added, specifying they are `protein`. The same
should be done for the metal ion:

        ...
        HID    Protein
        HDB    Protein
        .
        .
        .
        ZNB    Protein
        ...

6.    In the `specbond.dat` file a line specifying the bonds between protein and metal ion should be added:

        ...
        HDB    NE2    1    ZNB    ZNB    4    0.2    HDB    ZNB
        ...

First column specifies the first residue taking part in the bond, the second colums specifies the first atom that forms the bonds,
the third column specifies que ammount of special bonds this atom forms. The Fourth, fifth and sixth columns specify the same
information for the second residue and atom forming the bond. The seventh column specifies the distance &pm; 10% of the reference
distance under which, a bond will be formed for the specified atoms. The last two colums once again define the residues forming the bond.

Lastly, to start running simulations in Gromacs, you must edit the PDB file so that the newly parametrized residues have the new
residue names, e.g., HIS13 should be changed to HDB.

Once the editing is finished, you must indicate which is the force field you want to use. This can be done in two different ways:

1.    Define the `$PATH` in which the force field directory is stored and execute Gromacs from that directory:

    ```
    gmx=/usr/local/gromacs/gromacs-1ZE9/bin/gmx
    ```

2.    Store force field in the working directory, then when running `gmx pdb2gmx` you should select the force field present in the
working directory.

    Lastly, when running `gmx pdb2gmx`, the following line should be used:

    ```
    gmx pdb2gmx -f *pdb -ignh -o ${out} -p ${out} -merge all
    ```
