# Windows

#### Offline hard disk

run diskpart

```
#list all mounted disks
list disk

#select target disk
select disk [number]
offline disk
online disk 
#if not online again, the hard disk will not auto mounted next time
```

#### Reformat bootable USB to normal USB

Refer to [this](https://superuser.com/questions/860404/cannot-reformat-bootable-usb-drive-on-windows)

run diskpart as admin
```
#list all mounted disks
list disk

#select target USB
select disk [number]

#reformat
clean
create partition primary
select partition 1
format fs=fat32 quick
```

#### Fix for Edge clear all cache
Refer to [this](https://www.reddit.com/r/MicrosoftEdge/comments/1l7fnkd/workaround_fix_for_edge_resetting_all/). If Edge automatically reset all website and bookmarks, opten Task Manager and kill "Game Assist". Then re-open edge should work.
