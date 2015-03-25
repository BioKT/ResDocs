# Mac OS X hacks
These are just a few tricks that made my life incredibly easier when transitioning
from a Ubuntu Linux machine to working on a Mac desktop computer.

## Mounting ext2/ext3 volumes
This was useful for getting my backups to be read-write-able in my Mac. This is 
taken from the notes that Uditha Atukorala wrote for the 
[WireFrame](http://www.thewireframecommunity.com/node/174)

###1. Install MacFUSE

If you haven not already installed it download and install 
[MacFUSE](http://code.google.com/p/macfuse/downloads/list).

###2. Install FUSE - Ext2
Once you have MacFUSE download and install 
[fuse-ext2](http://sourceforge.net/projects/fuse-ext2/). Even though it 
says fuse-ext2, this one package gives both ext2 and ext3 read-write 
support.

After installation you should see both MacFUSE and fuse-ext2 icons 
in System Preferences. You now have support for ext2 and ext3 file 
systems. When you plug in an external ext2/ext3 partition it should 
automatically show up in Finder, mounted and ready to use.

If auto-mount is not giving you read/write access to ext2/ext3 
partitions then you will have to edit the auto-mount script for 
fuse-ext2 which can be found at `/System/Library/Filesystems/fuse-ext2.fs/fuse-ext2.util`.

```
$ sudo vi -c /System/Library/Filesystems/fuse-ext2.fs/fuse-ext2.util
```

Around line 207 (in function `Mount ()`) you will find the line 
`OPTIONS="auto_xattr,defer_permissions"`. Change that line to read as 
`OPTIONS="auto_xattr,defer_permissions,rw+"`.

```
...
function Mount ()
{
    LogDebug "[Mount] Entering function Mount..."
    # Setting both defer_auth and defer_permissions. The option was renamed
    # starting with MacFUSE 1.0.0, and there seems to be no backward
    # compatibility on the options.
    # OPTIONS="auto_xattr,defer_permissions"
    OPTIONS="auto_xattr,defer_permissions,rw+"

    # The local option is only enabled on Leopard. It causes strange
...
}
```

This last bit was what actually solved my problem.
