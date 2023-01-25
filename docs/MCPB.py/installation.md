# Installing MCPB.py

Since MCPB.py is a program included in AmberTools15 
and onward versions, the first step is to install 
AmberTools. AmberTools can be easily installed
as a conda environment:

```
$> conda create --name AmberTools21
$> conda activate AmberTools21
$> conda install -c conda-forge ambertools=21 compilers
$> conda update -c conda-forge ambertools
```

Once AmberTools is installed, the MCPB.py program
will be included by default.
