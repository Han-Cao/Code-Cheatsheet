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
