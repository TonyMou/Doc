### ubuntu 添加开机自启动脚本

#### Option 1: rc.local

ubuntu开机后会执行/etc/rc.local脚本，so 我们可以直接在/ect/rc.local中添加启动脚本。

注意，添加的语句要在exit 0之前，do not ask me why!

```
sudo vi /etc/rc.local


```

#### Option 2: init.d 添加一个开机启动服务

linux随机启动的服务程序都在/etc/init.d这个文件夹中，里面的文件全部是脚本文件，另外在/etc这个文件夹里还有诸如名为rc1.d, rc2.d一直到rc6.d的文件夹，这些都是linux不同的runlevel，我们一般进入的X windows多用户运行级别是第五级，也就是rc5.d，在这个文件夹里的脚本文件就是运行第5级时要随机启动的服务程序。需要注意的是，在每个rc(1-6).d文件夹下的文件其实是/etc/init.d文件夹下的文件的一个软连接，也就是说，在/etc/init.d文件夹下是全部的服务程序，而每个rc(1-6).d文件值链接它自己启动需要的相应服务程序。

另外，在rc5.d中，每个链接的名字都是以S或K开头的，S开头的表示是系统启动时要随机启动的，K开头的是不随机启动的。这样你可以知道，如果我要哪个服务随机启动，就把它的第一个字母K改为S就可以了；反之，S改为K就是这个服务不随机自启动了。

ubuntu系统运行级别：

0 - 系统停机状态
1 - 单用户或系统维护状态
2~5 - 多用户状态
6 - 重新启动

下面是添加开机服务的方法：

1. 先将脚本复制或软连接到/etc/init.d/目录下，比如我在~/目录下创建一个start-kobe的文件，用于开机自启动zookeeper。

```
#!/bin/bash

/opt/zookeeper-3.4.8/bin/zkServer.sh start

```

2. chmod修改start-zk.sh脚本可执行权限

```
chmod +x start-zk.sh

```

3. 执行命令，将脚本添加到初始化执行队列中：

```
update-rc.d start-zk.sh defaults

```

4. 卸载自启动脚本的方法：

```
cd /etc/init.d
sudo update-rc.d -f start-zk.sh remove

```

更多update-rc.d命令请参考相关文档。

#### Option 3: 添加登录时运行脚本

登录时运行的程序：

用户登录时，bash首先自动执行系统管理员建立的全局登录脚本：/etc/profile. 然后bash在用户起始目录下按顺序查找三个特殊文件中的一个：/.bash_profile, /.bash_login, /.profile， 注意，只会执行最先找到的一个。

因此，可以根据实际需要在上述文件中加入命令就可以实现用户登录时自动运行某些程序。

退出登录时自动运行脚本：

退出登录时，bash自动执行个人的退出登录脚本/.bash_logout.