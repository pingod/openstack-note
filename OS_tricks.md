openstack tricks
---

###使用curl发送消息：
+ [root@controller ~(keystone_admin)]# nova --debug image-create 6c2504f4-efa9-47ec-b6f4-06a9fde8a00b nova_100_new_01
+ 获取token：  
```xml
curl -i http://186.100.8.214:35357/v2.0/tokens -X POST -H "Content-Type: application/json" -H "Accept: application/json" -H "User-Agent: python-novaclient" -d '{"auth": {"tenantName": "admin", "passwordCredentials": {"username": "admin", "password": "admin"}}}'
```
+ 获取虚拟机信息：  
```xml
curl -i http://186.100.8.214:8774/v2/86196260e1694d0cbb5049cfba3883f8/servers/6c2504f4-efa9-47ec-b6f4-06a9fde8a00b -X GET -H "X-Auth-Project-Id: admin" -H "User-Agent: python-novaclient" -H "Accept: application/json" -H "X-Auth-Token: "
```
+ 创建image：  
```xml
curl -i http://186.100.8.214:8774/v2/86196260e1694d0cbb5049cfba3883f8/servers/6c2504f4-efa9-47ec-b6f4-06a9fde8a00b/action -X POST -H "X-Auth-Project-Id: admin" -H "User-Agent: python-novaclient" -H "Content-Type: application/json" -H "Accept: application/json" -H "X-Auth-Token: " -d '{"createImage": {"name": "nova_100_new_01", "metadata": {}}}'
```

+ json可读显示
```xml
curl命令 | python -mjson.tool
```

###查看mysql数据库：
+ 登陆：  
$ mysql -uroot -p
+ 查询数据库：  
mysql>show databases;
+ 查看某个数据库：  
mysql>use database名;
+ 查看数据库中的表：  
mysql>show tables; 
+ 查看表中的数据：  
mysql>select * from table名;
+ 查看表结构：  
mysql>desc table名;

###pdb调试
两种方式运行：
+ python -m pdb myscript.py
+ 代码中添加pdb
```python
          import pdb
          pdb.set_trace()
```
+ openstack  
可以使用上述方法2，添加2行代码。然后手工运行服务。  
pdb支持的指令以及指令详细用法可以通过help查看。

### pdb调试unit test
1. 如上增加pdb代码  
2. 运行nosetests  
   "nosetests volume/test_cinder.py --nocapture"  
备注：  
在unit test中增加打印代码，也要“--nocapture"才能正常打印。  

### 邮件搜索  
http://openstack.markmail.org/

### 单元测试  
```shell
nosetests want_test_file.name
nosetests want_test_file.name:class.method
```
解决proxy后run_tests.py运行失败的问题：  
```shell  
pip install --upgrade -r test-requirements.txt
pip install --upgrade -r requirements.txt
然手运行nosetest进行测试。
```
```
现在最新的运行单元测试的方法：
tox -e py27
tox -e pep8
安装tox时注意，1.7的tox存在bug，因此使用如下命令按照
pip install --upgrade "tox>=1.6,<1.7"
```

### 代码提交
+ 修改加提交代码流程
git checkout -b 分知名（bp名或bug名） origin/master  
修改代码  
git commit  
git commit --amend  
git review  
+ 第n次修改代码过程
修改代码  
git commit  
git reabse -i HEAD~2   合并两次提交(squash新的commit)  
git review  

### tempest
http://www.ibm.com/developerworks/cn/cloud/library/1403_liuyu_openstacktempest/

### 修改openstack所属公司  
https://review.openstack.org/#/c/87957/

###  os-update_readonly_flag
```shell
curl -i http://186.100.8.138:8776/v1/60c9922640794828b62f1663682154c8/volumes/da4f85f3-df06-4065-a435-1eed077cb4be/action -X POST -H "X-Auth-Project-Id: admin" -H "User-Agent: python-cinderclient" -H "Content-Type: application/json" -H "Accept: application/json" -H "X-Auth-Token: token" -d '{"os-update_readonly_flag": {"readonly": true}}'
```

