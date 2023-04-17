# Parametrizing an amino acid using AmberTools21

In order to paramterize an amino acid, the first thing we need
is an structure. Conventionally, the structure used to parametrize
an amino acid is to build an structure with the amino acid with
ACE and NME caps, e.g., if I want to parametrize a methionine my
structure will be ACE-MET-NME. To do so, we need to build the amino
acid using the building software of our liking (Amber's tleap, 
Gaussview or any other works). The structure built for this tutorial
can be found here [MET.xyz](https://drive.google.com/file/d/14VTesUlBdBR3O2gln5xxqVhOX5dlqAq7/view?usp=sharing).
Mind that atom numbers are ordered to match the sequence (ACE-MET-NME), and
that in the case of the aminoacid, the atom number of the atoms of MET
match the order in which they appear in GROMACS. The order of the atoms
will be important for future steps.

Once the structure is built, one must follow the steps mentioned in the
artcile of the force field selected. To easily find which paper one should
follow, one can directly find it referenced in the `forcefield.doc` file
present in each force field directory present in the `${gromacs-path}/share/
gromacs/top/${force-field}.ff` directory. Since in this tutorial we are
interested in obtaining parameters for the Amber99SB*-ILDN force field,
[this](https://onlinelibrary.wiley.com/doi/full/10.1002/1096-987X%28200009%2921%3A12%3C1049%3A%3AAID-JCC3%3E3.0.CO%3B2-F)
is the article we should be looking at. In this article, it is clearly 
stated that RESP charges were calculated following these steps: 
`quantum-mechanical optimizations were performed for all the compounds 
using the 6-31G* basis set (...) electrostatic potentials were then 
calculatedat the same level for the minimized energy`.
the minimized geometry


