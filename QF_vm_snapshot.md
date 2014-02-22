快照分类
-----
+ 磁盘快照  
对磁盘数据进行快照。主要用于虚拟机备份等场合。  
    + 按快照信息保存为可以可以分为：  
        + 内置快照  
            快照数据和base磁盘数据放在一个qcow2文件中。  
        + 外置快照  
            快照数据单独的qcow2文件存放。  
    + 按虚拟机状态可以分为:  
        + 关机态快照  
            数据可以保证一致性。  
        + 运行态快照  
            数据无法保证一致性，类似与系统crash后的磁盘数据。使用是可能需要fsck等操作。  
    + 按磁盘数量可以分为        
        + 单盘  
            单盘快照不涉及原子性。  
        + 多盘  
            涉及原子性。主要分两个方面：1.是所有盘快照点相同 2.所有盘要么都快照成功，要么都快照失败。  
            主要依赖于qemu的transaction实现。  
        
+ 内存快照  
对虚拟机的内存/设备信息进行保存。该机制同时用于休眠恢复，迁移等场景。    
主要使用qemu的savevm（migrate to file）实现。    
只能对运行态的虚拟机进行。 

+ 检查点快照
同时保存虚拟机的磁盘快照和内存快照。用于将虚拟机恢复到某个时间点。可以保证数据的一致性。  


内置快照
-----
### 利用qemu-img进行磁盘内置快照 
```shell
qemu-img snapshot -c snapshot01 test.qcow2  //创建
qemu-img snapshot -l test.qcow2             //查看
qemu-img snapshot -a snapshot01 test.qcow2  //revert到快照点
qemu-img snapshot -d snapshot01 test.qcow2  //删除

```
### 利用Libvirt进行磁盘内置快照  
```xml
snapshot.xml
  <domainsnapshot>
    <name>snapshot01</name>
    <description>Snapshot of OS install and updates by boh</description>
    <disks>
      <disk name='/data/os-multi/controller.qcow2'>
      </disk>
    </disks>
  </domainsnapshot>
  
virsh snapshot-create controller snapshot.xml  //创建快照。快照元信息在/var/lib/libvirt/qemu/snapshot/（destroy后丢失）
virsh snapshot-list controller --tree          //树形查看快照。
virsh snapshot-current controller              //查看当前快照
virsh snapshot-revert controller snapshot02    //恢复快照
virsh snapshot-delete controller snapshot02    //删除快照

功能参数：
    --quiesce        quiesce guest's file systems
    --atomic         require atomic operation

```


### 参考
[Atomic Snapshots of Multiple Devices]:http://wiki.qemu.org/Features/SnapshotsMultipleDevices
[Snapshots]:http://wiki.qemu.org/Features/Snapshots
[Libvirt snapshot]:http://wiki.libvirt.org/page/Snapshots
[Fedora virt snapshot]:https://fedoraproject.org/wiki/Features/Virt_Live_Snapshots
[Libvirt live snapshot]:http://kashyapc.com/2012/09/14/externaland-live-snapshots-with-libvirt/
[kvm快照浅析]:http://itxx.sinaapp.com/blog/content/130
[1]:http://blog.sina.com.cn/s/blog_53ab41fd01013rc0.html
[2]:http://blog.csdn.net/gg296231363/article/details/6899533


```
optionally - use the guest-agent to tell the guest OS to quiesce I/O
tell qemu to migrate guest memory to file; qemu pauses guest
for each disk:
  tell qemu to pause disk image modifications for that disk
libvirt resumes qemu (but I/O is still frozen)
for each disk:
  libvirt creates the snapshot
  if the snapshot involves updating the backing image used by qemu:
    pass qemu the new fd for the disk image
  tell qemu to resume disk I/O on that disk

where once again, reverting to a system restore point is:

for each disk:
  revert back to disk snapshot point
tell qemu to do incoming migration from file
```