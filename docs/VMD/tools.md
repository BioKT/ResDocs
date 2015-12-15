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
# Making videos in VMD 
VMD has a built in tool called Movie Maker. You must use it to generate a 
video according to your particular taste. You can find some instructions
[here](http://www.ks.uiuc.edu/Research/vmd/plugins/vmdmovie/). 
One possibility is that you save the files for each of the snapshots of the
simulation, instead of letting VMD produce the movie and remove the files
for you. If you do not do that, then you can tweak things a bit, in terms
of using different movie formats or tuning the bitrate. Below is an example
of the command that you can invoke for generating the video.

```
ffmpeg -i filename.%05d.ppm -r 25 -an -b 10000k -bt 10000k moviename.mpg
```

Clearly, you will need the `ffmpeg` program for doing this, which in a Mac
you can obtain from MacPorts. Then the `%0.5d` in the filename just corresponds
to the different files written by VMD that you want to process (be careful with 
format).
