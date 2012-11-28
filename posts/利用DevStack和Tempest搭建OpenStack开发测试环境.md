# 利用*DevStack*和*Tempest*搭建**OpenStack**开发测试环境 #

**版权声明** 

可以任意转载，转载时请务必以超链接形式标明文章原始出处和作者信息及本版权声明

by **Yujie Du**  is licensed under a [Creative Commons Attribution-ShareAlike 3.0 Unported License](http://creativecommons.org/licenses/by-sa/3.0/)

**日期:**  
12:07 上午 星期四, 十一月 29, 2012

**版本:**
0.1



## 配置OpenStack测试环境 ##

上次[@trystack](http://weibo.com/trystack) 活动时请来了[Intel的兄弟](http://weibo.com/1716287123/z66aDl9xC)来分享了一下[OpenStack集成测试环境Tempest](http://vdisk.weibo.com/s/jgf1F)，很多朋友私下问能否整个中文文档分享一下，刚好内部培训也要用我就先整理一下。

在测试之前，你需要搭建一个OpenStack环境。最简单的方式是直接使用[trystack.cn](trystack.cn)，当然你也可以用 [DevStack](http://devstack.org/) 搭建一个单机版的测试环境。

DevStack是一套用来给开发人员快速部署Openstack开发环境的脚本，你可以直接克隆DevStack仓库代码来获取：

`ben@trystack:~/repo:$ git clone git://github.com/openstack-dev/devstack`

现在你已经有了keystone,nova,glance等OpenStack服务了，不过接下来还需要配置一下测试环境。

**配置DevStack环境**

创建你的OpenStack开发测试环境之前不妨看下DevStack的*localrc*文件，如果没有该文件可以手动创建一个：


`API_RATE_LIMIT=False`

`MYSQL_PASSWORD=pass`

`RABBIT_PASSWORD=pass`

`SERVICE_PASSWORD=pass`

`ADMIN_PASSWORD=pass`

`SERVICE_TOKEN=servicetoken`

第一行取消Nova API服务的默认的流量限制中间件，因为Tempst在测试时会在很短时间内分发成百上千的API请求，如果不取消的话Tempest会花很长时间运行，并且你很可能会得到测试失败的overLimitFault消息提示。其他几行把密码变量设置为*`pass`*，以便轻松进行测试。最后一行目前需要设置一些服务，但很快就会弃用。

执行以下命令使得nova能够在单一主机上改变实例大小：

`$ sudo openstack-config --set /etc/nova/nova.conf DEFAULT allow_resize_to_same_host True`

`$ for svc in api compute scheduler; do sudo systemctl restart openstack-nova-$svc.service; done`

**安装OpenStack Grizzly版第一个 milestone 环境**

`$ cd devstack/`

`$./stack.sh`

维护一个支持多 Linux 发行版的脚本需要很多工作，为了保持简单，DevStack 目前只支持 Ubuntu 12.4和Fedora 16 两个发行版。运行 stack.sh 脚本会依次设定 MySQL, RabbitMQ, OpenStack Dashboard 和 Keystone 的密码，密码输入后 stack.sh 脚本会自动开始安装必要的软件包和库并下载最新的 OpenStack 及其组件代码，整个过程自动完成无需干预，第一次执行时可能需要十几分钟。

然后运行一下info.sh脚本即可验证开发测试环境是否正常。如果一切顺利，你就已经搭建起来了一套完整的OpenStack 环境，也可以通过查看logs文件验证。
 
## 使用Tempest测试OpenStack  ##
Testing OpenStack with Tempest

**下载tempest代码**

创建临时目录：

`$ mkdir /tmp/tempest`

`$ cd /tmp/tempest`

克隆相关代码：

`$ git clone https://github.com/openstack/tempest.git`

`$ cd tempest`

**创建tempest配置文件**

Tempest会执行一系列的API命令，所以需要配置相关信息以便找到相关服务。此外，Tempest还需要知道DevsStack下载的和Glance服务里安装的镜像的UUID。
创建配置文件过程非常简单，只需复制Tempest目录下的/etc/tempest.conf.sample即可：

`$ cp etc/tempest.conf.sample etc/tempest.conf`


下一步，你需要询问Glance API服务器以获取用户信息以及测试用AMI镜像的UUID，

`$ glance -I **trystack** -K pass -T **trystack** -N http://127.0.0.1:5000/v2.0 -S keystone index | grep ami | cut -f1 | awk '{print $1}'`

这时不妨看下你所创建的配置文件：

`$cat etc/tempest.conf`

**开始工作：**
接下来要做的事情就是一遍又一遍的跑Tempest:-)

`$ nosetests tempest`

也可以单独运行某个模块：

$ nosetests tempest.test.test_flavors 

下一篇：Tempest

- Tempest介绍
	- 技术架构
	- 代码流程
- 参考资料


	本文是内部培训文章的第二篇，上次介绍过《如何参与OpenStack贡献》之后我们来看看OpenStack社区是如何进行集成测试的，通过本次培训你将能够进一步熟悉社区开发流程。

## 项目背景 ##
  
## 当前状态 ##

## 安装使用流程 ##

## 所做工作 ##
	- 技术架构
	- 代码流程
	- 结果分析

后记 ：
使用 DevStack 还是不清楚整个部署过程是怎样的；
DevStack也可以部署多节点
## 参考资料 ##

http://www.slideshare.net/koolhead17/pycon-india

https://jenkins.openstack.org/view/Tempest/job/gate-tempest-devstack-vm/configure

http://www.vpsee.com/tag/devstack/
http://blog.csdn.net/weiyuanke/article/details/7639849
blog.xuite.net/dain198/study/61914788-Openstack+Ubuntu+12.04+快速架設+(by+DevStack)+以及後續設定+(上)
