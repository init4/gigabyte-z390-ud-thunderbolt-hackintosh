# Rebuilding a MacOS software RAID volume

I wrote this guide to save myself having to figure out the right commands next time a disk fails. I was surprised that MacOS wont actually alert you when a disk has failed :-/ . If someone knows how to make it do that, let me know (I guess maybe a MacOS Server config?). 

## Identify the faulty drive

First we need to figure out which disk has failed.
```
starscream[/scratch/tmp/current] diskutil checkraid

===============================================================================
Name:                 RAID_set_2
Unique ID:            A91EE267-9A0E-464F-A839-07A82ED833B1
Type:                 Mirror
Status:               Degraded
Size:                 4.0 TB (4000443039744 Bytes)
Rebuild:              manual
Device Node:          disk8
-------------------------------------------------------------------------------
#  DevNode   UUID                                  Status     Size
-------------------------------------------------------------------------------
0  disk2s2   C611E4FB-3382-4B7F-9512-4E051FFF41F3  Online     4000443039744
1  disk3s2   06173980-BF98-422F-AC36-818D54349EDD  Failed     4000443039744
===============================================================================
starscream[/scratch/tmp/current] 
```
Notice the failed disk in the RAID_set_2 volume. Pay attention to the logical disk number (disk3) and its UUID.

## Remove the failed drive from the volume

Now we detach the failed disk from the RAID_set_2 volume. Triple check that you are really removing the right disk..
```
starscream[/scratch/tmp/current] diskutil appleraid remove 06173980-BF98-422F-AC36-818D54349EDD disk8
Started RAID operation on disk8
Removing disk from RAID
Changing the disk type
Finished RAID operation on disk8
starscream[/scratch/tmp/current] 
```
Verify that it's gone-
```
diskutil checkraid
AppleRAID sets (2 found)
===============================================================================
Name:                 RAID_set_2
Unique ID:            A91EE267-9A0E-464F-A839-07A82ED833B1
Type:                 Mirror
Status:               Online
Size:                 4.0 TB (4000443039744 Bytes)
Rebuild:              manual
Device Node:          disk8
-------------------------------------------------------------------------------
#  DevNode   UUID                                  Status     Size
-------------------------------------------------------------------------------
0  disk2s2   C611E4FB-3382-4B7F-9512-4E051FFF41F3  Online     4000443039744
===============================================================================
```

## (Re)Initialise the replacement drive

This particular drive still seems to respond to commands and isn't making any weird noises, so I think its still working. So I'm just going to re-initialise it and put it back into the volume.
```
starscream[/scratch/tmp/current] diskutil eraseDisk HFS+ BLANK /dev/disk3
Started erase on disk3
Unmounting disk
Creating the partition map
Waiting for partitions to activate
Formatting disk3s2 as Mac OS Extended with name BLANK
Initialized /dev/rdisk3s2 as a 4 TB case-insensitive HFS Plus volume
Mounting disk
Finished erase on disk3
starscream[/scratch/tmp/current] 
```
If it fails again I will actually replace the physical drive. This volume is just full of disposible containers/VMs anyway..

## Add the replacement drive to the volume

Now add the initialised disk back into the volume.
```
starscream[/scratch/tmp/current] diskutil addtoRAID member disk3 A91EE267-9A0E-464F-A839-07A82ED833B1
Started RAID operation on disk8
Unmounting disk
Repartitioning disk3 so it can be in a RAID set
Unmounting disk
Creating the partition map
Adding disk3s2 to the RAID Set
Finished RAID operation on disk8
starscream[/scratch/tmp/current] 
```
Now you can check that the volume is being synchronised with the checkraid command-
```
starscream[/scratch/tmp/current] diskutil checkraid
AppleRAID sets (2 found)
===============================================================================
Name:                 RAID_set_2
Unique ID:            A91EE267-9A0E-464F-A839-07A82ED833B1
Type:                 Mirror
Status:               Degraded
Size:                 4.0 TB (4000443039744 Bytes)
Rebuild:              manual
Device Node:          disk8
-------------------------------------------------------------------------------
#  DevNode   UUID                                  Status     Size
-------------------------------------------------------------------------------
0  disk2s2   C611E4FB-3382-4B7F-9512-4E051FFF41F3  Online     4000443039744
1  disk3s2   AE52533C-796A-4BB1-822C-0AEF8BE3C37C  5% (Rebuilding)4000443039744
===============================================================================
starscream[/scratch/tmp/current] 
```

MacOS RAID is a nice little underappreciated feature. While its not as good as a full-blown zfs, it's been really solid for me. Hopefully this info has been useful for you! 🍺🍺

