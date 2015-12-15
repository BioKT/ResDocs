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
