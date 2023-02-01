# 使用 Red Hat Satellite 6 进行配置

> 原文：<https://developers.redhat.com/blog/2017/11/30/provisioning-red-hat-satellite-6>

Satellite 是 Red Hat 的内容管理、供应、配置管理和生命周期管理解决方案，有助于保持您的基础设施高效、安全地运行，同时降低成本和总体复杂性。无论您的环境是什么样的，您都可以从 Satellite 提供的配置管理功能中受益。

在[这个短片](https://www.youtube.com/watch?v=0w8eylMqgoU)中，我们讨论了 Satellite 6 最受欢迎的功能之一——机器供应，并演示了卫星供应工作流程:

1.  选择用于部署新计算机的工具集:
    *   Hammer 命令行界面(CLI)
    *   网络图形用户界面(GUI)
    *   卫星 6 应用程序接口
2.  登录卫星并提供主机配置信息:
    *   主机名
    *   主机将驻留的组织
    *   新主机的位置
    *   部署平台。Satellite 6 可以提供以下任何环境:物理机、虚拟化基础设施或云空间中的服务器。在我们的示例中，我们从 Red Hat 虚拟化中部署了一个新实例。我们首先选择与 Red Hat 虚拟化相关的计算资源，并查看与该资源相关的计算配置文件。计算配置文件是预设的机器类型，用于定义 CPU 和内存起始点。
3.  定义主机内容并配置新主机，指定内容视图的来源和现有配置信息的位置(如果适用)。指定主机组允许您从更广泛的一组计算机中继承通用配置信息。
4.  Satellite 6 包括 Puppet，并允许将 Puppet 模块加载到您的内容视图中。您可以选择 Puppet 类来应用和定制您部署的每台机器。
5.  定义网络属性:
    *   主机将连接到的设备
    *   设备名称
    *   子网络
    *   域名系统(DNS)域
6.  指定操作系统信息:
    *   新主机的操作系统
    *   安装介质的位置
    *   分区表类型
    *   配置模板，其中包含您的 kickstarts 以及用于自定义的任何配置前和配置后脚本。
7.  选择“提交”按钮来配置您的新机器。虚拟化平台创建您的新虚拟机，动态主机配置协议(DHCP)确定主机预留的存在或定义 IP 地址。然后，主机使用预启动执行环境(PXE)启动过程启动，并与卫星小文件传输协议(TFTP)服务器通信以提取初始映像。最后，主机将使用您指定的初始配置启动，并在 DNS 中验证主机地址。

简而言之，一旦设置了后端组件，只需按一下按钮，就可以配置根据您的特定环境需求定制的新机器。使用此工作流，您可以随时快速一致地部署更多虚拟机，从而节省您的宝贵时间和精力，同时提供更可靠的环境。

想在云生命周期管理战略中加入卫星应用吗？

这不是红帽的第一次竞技。请与您的客户经理讨论红帽探索会议，了解更多有关集成卫星的信息，或者直接联系 redhat.com/consulting，咨询相关问题。红帽咨询是来帮忙的。

**请务必观看 Red Hat Consulting 白板视频系列的另一部分，可在[red.ht/RHCVideos](https://www.youtube.com/playlist?list=PLbMP1JcGBmSEc5SmaJ1CkCSn9zIULu1p2)观看。**

* * *

**如果你知道 Linux 的基本命令，那么下载** [**高级 Linux 命令备忘单**](https://developers.redhat.com/cheat-sheets/advanced-linux-commands/) **，这个备忘单可以帮助你把你的技能提升到一个新的水平。**

*Last updated: November 29, 2017*