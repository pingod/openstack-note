###特性介绍  
qemu在1.5/1.6已经支持了raw和qcow2的trim特性[1]。  
本文主要介绍在openstack中的实现。

###接口变化  
接口名：
v2/​{tenant_id}​/servers/​{server_id}​/os-volume_attachments
通过参数dict参数volumeAttachment传入是否开启trim。

###代码流程  


[1]:qemu_feature_trim.md