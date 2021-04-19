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

