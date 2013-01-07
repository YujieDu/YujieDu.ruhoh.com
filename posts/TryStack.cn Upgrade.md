---
title: TryStack.cn升级记（1）
date: '2013-01-07'
description: 使用MaaS和Juju部署OpenStack服务
categories: How To
tags: [OpenStack, trystack, MaaS, Juju, 持续交付 ]
---
# TryStack.cn升级记（1） #

元旦一直在忙[Trystack.cn](http://www.trystack.cn) 升级改造方案，主要是希望能够提高[@trystack](http://weibo.com/trystack)的交付能力，通过构建部署流水线，将手工操作变成一种可靠、可预期、可视化的过程，期间也和trystack社区里朋友一起交流过。

我们的**目标**是希望让所有的测试环境（包括持续集成环境）都与生产环境相似，特别是它们的管理方式，包括所有的资源和配置信息。由于之前时没有考虑太多的**持续集成**问题，所以得到的建议是如果要想升级改造，还是需要先分析一下目前的痛处是什么，我想到了一些也希望能和大家一起讨论一下：

1. 首先想到一个问题是[@trystack](http://weibo.com/trystack) 的**授权访问**的问题，虽然我们相信无罪假设，可有关部门万一不给机会解释一不小心就会陷入万劫不复的深渊，好心未必做成好事。所以需要一个自动化的访问控制流程，只有通过审批才能对[@trystack](http://weibo.com/trystack) 基础设施做出修改。
	
2. 另外，计划外的变更会带来不可预知的后果，所以对生产环境和测试环境的变更请求应该执行一个**变更流程**。所以就需要对[@trystack](http://weibo.com/trystack) 上的#OpenStack#IaaS平台的基础设施进行修改，比如防火墙规则。由于没有变更流程，所以也无法从版本库抓取基础设施变更项进行部署。

3. 还有就是节后又有一批服务器上线，[@trystack](http://weibo.com/trystack) 目前还缺乏统一的部署安装以及配置管理。这些重复性的操作有必要用自动化的方式来解决，这样即减少出错的几率又便于管理。特别是**配置管理**自动化，否则很难保证环境的一致性。无论是[@trystack](weibo.com/trystack) 还是生产环境中都需要对网络基础设施进行配置管理。前段时间我写了一个[Quantum方面的介绍](http://www.tektalk.org/2012/12/23/19893/)，小的实验可以在本地虚拟环境下测试，但在多宿主环境下是需要使用多个隔离网络的，包括管理网络、生产环境网络和备份网络的物理隔离等，我们需要对所有网络配置信息，包括路由进行管理和监控。

以上这些其实无非都是想*缩短从想法到可以看到实现的商业价值之间的周期*，基础当然是**持续集成**。通过高度的自动化来**构建部署流水线**，使得发布过程可重复，更可靠。接下来我会逐步的把各个环节的文档整理出来，从自动化部署和配置管理的角度实现如何把创建和维护[@trystack](webibo.com/trystack)所需的OpenStack基础环境都通过**版本控制**管理起来，同时通过开放相关文档和自动化脚本让更多人受益。
	
## 第一步 自动化部署方案Metal as a Service（MaaS） ##

由于[@trystack](http://weibo.com/trystack)平台选择的是Ubuntu 12.04，Ubuntu本身提供了两个相关服务：MaaS和Juju，MaaS用于部署Nodes，Juju来做控制，我们先来看一下他们如何工作的。

**为裸机配置安装MAAS**

现在有多种方式来为裸机部署Ubuntu等操作系统，如Cobbler和Kickstart。Ubuntu提供了一个方便的工具MAAS来为数据中心的裸机部署服务器，也即是Metal-as-a-Service。这个工具允许我们简单的设置一个网络启动环境，然后我们可以给它分配任务，安装OpenStack相关服务，如Compute或Dashboard等。

首先，MAAS提供了PXE boot服务，使用这些软件包安装是很容易的，在安装时完成相关配置即可。

主要的命令行工具是maas，需要用它来创建一个管理员用户trystack，该用户在需要时可以用来创造更多的帐户。

一切配置完毕，会执行一个pull请求从ubuntu.com抓取ISO文件。似乎这项工作每周运行一次，但安装后我们必须先手动启动。

其次，MAAS可以帮助我们在网络中的裸机环境下配置主机，意味着从加电开始这些主机就已经随时听候调遣。

第一步，通知MAAS哪些节点需要使用MAAS安装，通过这个服务来支持它。同时会发送一些信息给MAAS，用来识别这些节点（特别是节点接口上的MAC地址）。一旦MAAS识别出这些节点，我们就能启动它，MAAS会通过Wake-On-LAN(WOL)自动启动，然后引导该节点进入OS安装环境。引导之后就可以执行PXE boot来安装操作系统，并通过Juju准备进一步的工作。


Ubuntu 提供Juju这个工具，不仅可以安装包，并且同样可以通过charms（Charms是一组关于如何安装和配置这些服务的脚本和描述信息）安装配置相关服务。例如，一个Wordpress的charm，不但可以安装Wordpress的PHP文件，同样可以安装Wordpress所需要的MySQL数据库，或为其配置负载均衡。

**为Trystack部署OpenStack服务**

Juju安装成功配置好MAAS后，就可以配置Juju安装我们的OpenStack环境了。

首先建立一个配置文件，Juju安装OpenStack组件时会用到，这极大的提高了我们使用Juju时的灵活性性。

为OpenStack配置做好准备后，就可以开始使用Juju。第一步是引导环境，设置一台服务器用于部署我们的环境。紧接着，开始一个接一个的安装服务。 不过现在好像Juju仅支持安装独立的服务，所以每次Juju部署都需要一个新的节点。

通过部署服务，就简单定义好了服务间的关系，或者说把服务连接到一起了。例如，连接keystone服务器和MySQL服务器。同样还需要连接keystone到compute，glance等，它们都依赖于OpenStack的身份服务。同样的，依赖于mysql的服务也连接起来。这个过程一直持续到所有的关系都建立起来。

完成之后，为了让Juju决定在哪里安装服务，需要找到是哪个节点安装了OpenStack Dashboard，可以通过查看 openstack-dashboard状态信息，得到URL。

接下来就可以通过Juju来为[@trystack](http://weibo.com/trystack)提供动态添加计算能力、管理MySQL集群等服务了。

相关内容先整理到这，等服务器上架再来详细的安装说明。

祝好

[@ ben_杜玉杰](http://weibo.com/u/1716287123)

参考：

1. [Ubuntu UDS R - Juju Charm Workshop with Marco Ceppi ](https://www.youtube.com/watch?v=9rswMgglc_o)

2. [Testing MaaS](https://wiki.edubuntu.org/SecurityTeam/TestingMAAS)

3. [持续交付](http://www.continuousdelivery.info/)