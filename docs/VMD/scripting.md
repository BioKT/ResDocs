# VMD scripting
It is relatively easy to use the command line instead of the GUI in VMD. This is
particularly useful when one needs to load multiple molecules, or make multiple
visualizations look the same (e.g. making multiple molecules look like NewCartoon
but changing their colour).

### Loading a molecule

```
mol new file.pdb
```

### Applying a visualization style

```
mol modstyle 0 0 NewCartoon
```

### Changing colour

```
mol modcolor 0 0 ColorID 2
```

### Loading a trajectory

```
mol addfile traj_comp.xtc
```

### Loops in VMD
The example below is for changing the colour of five different molecules so that
each has a different colour.

```
for {set x 0} {$x <= 5} {incr x} {
mol modcolor 0 $x ColorID $x 
}
```
