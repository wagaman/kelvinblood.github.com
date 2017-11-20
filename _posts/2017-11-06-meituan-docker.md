---
layout: post
title: 美团点评业务之技术解密，日均请求数十亿次的容器平台 | 转
category: tech
tags: docker
---
![](https://cdn.kelu.org/blog/tags/docker.jpg)


转载自 [InfoQ](http://www.infoq.com/cn/articles/technology-decryptionof-meitaun-services-review)

本文介绍美团点评的 Docker 容器集群管理平台（以下简称容器平台）。该平台始于 2015 年，基于美团云的基础架构和组件而开发的 Docker 容器集群管理平台。目前该平台为美团点评的外卖、酒店、到店、猫眼等十几个事业部提供容器计算服务，承载线上业务数百个，容器实例超过 3 万个，日均线上请求超过 45 亿次，业务类型涵盖 Web、数据库、缓存、消息队列等等。

## 容器到来之前

在容器平台实施之前，美团点评的所有业务都是运行在美团私有云提供的虚拟机之上。美团点评大部分的线上业务都是面向消费者和商家的，业务类型多样，弹性的时间、频度也不尽相同，这对弹性服务提出了很高的要求。

在这一点上，虚拟机已经难以满足需求，主要体现以下两点：

*   虚拟机弹性能力较弱，部署效率低，认为干预较多，可靠性差。

*   IT 成本高。由于虚拟机弹性能力较弱，业务部门为了应对流量高峰和突发流量，普遍采用预留大量机器和服务实例的做法。资源没有得到充分使用产生浪费。

(点击放大图像)

[![](https://cdn.kelu.org/blog/2017/11/meituan/1.jpg)](https://cdn.kelu.org/blog/2017/11/meituan/1.jpg)

## 容器时代

### 架构设计

美团点评将容器管理平台视作一种云计算模式，因此云计算的架构同样适用于容器。

如前所述，容器平台的架构依托于美团私有云现有架构，其中私有云的大部分组件可以直接复用或者经过少量扩展开发，如图所示。

(点击放大图像)

[![](https://cdn.kelu.org/blog/2017/11/meituan/2.jpg)](https://cdn.kelu.org/blog/2017/11/meituan/2.jpg)

图 1\. 美团点评容器管理平台架构

整体架构上与云计算架构是一致的，自上而下分为业务层、PaaS 层、IaaS 控制层及宿主机资源层。

*   **业务层**：代表美团点评使用容器的业务线，他们是容器平台的最终用户。
*   **PaaS 层**：使用容器平台的 HTTP API，完成容器的编排、部署、弹性伸缩，监控、服务治理等功能，对上面的业务层通过 HTTP API 或者 Web 的方式提供服务
*   **IaaS 控制层**：提供容器平台的 API 处理、调度、网络、用户鉴权、镜像仓库等管理功能，对 PaaS 提供 HTTP API 接口
*   **宿主机资源层**：Docker 宿主机集群，由多个机房，数百个节点组成。每个节点部署 HostServer，Docker、监控数据采集模块，Volume 管理模块，OVS 网络管理模块，Cgroup 管理模块等。容器平台中的绝大部分组件是基于美团私有云已有组件扩展开发的，例如镜像仓库、平台控制器、HostServer、网络管理模块。容器平台的技术栈，核心组件，包括平台控制器，网络服务，调度器等都是自研开发的。Docker 镜像仓库基于 Openstack 的 Glance 扩展开发，监控系统有使用 Falcon，CAT；服务治理、服务发现、服务负载均衡是使用的美团自研系统。开发语言主要是 Python 和 Golang。

### 容器网络

## 高性能、高弹性的架构设计

容器平台在网络方面复用了美团云网络基础架构和组件，逻辑架构设计如图所示。

(点击放大图像)

[![](https://cdn.kelu.org/blog/2017/11/meituan/3.jpg)](https://cdn.kelu.org/blog/2017/11/meituan/3.jpg)

图 2\. 美团点评容器平台网络架构

在数据平面，我们采用万兆网卡，结合 OVS-DPDK 方案，并进一步优化单流的转发性能，将几个 CPU 核绑定给 OVS-DPDK 转发使用，需要少量的计算资源即可提供万兆的数据转发能力。OVS-DPDK 和容器所使用的 CPU 完全隔离，因此也不影响用户的计算资源。控制平面我们使用 OVS 方案。所谓 OVS 方案是在每个宿主机上部署一个软件的 Controller，动态接收网络服务下发的网络规则，并将规则进一步下发至 OVS 流表，决定是否对某网络流放行。

## 自研配置模式 MosBridge

在 MosBridge 之前，我们配置容器网络使用的是 None 模式。在实践中，我们发现 None 模式存在一些不足：

1.  容器刚启动时是无网络的，一些业务在启动前会检查网络，导致业务启动失败；
2.  网络配置与 Docker 脱离，容器重启后网络配置丢失；
3.  网络配置由 Host-SRV 控制，对于以后网络功能的升级和扩展带来很多不便，例如对容器增加虚拟网卡，或者支持 VPC。

为了解决这些问题，我们基于 Libnetwork，开发了 MosBridge – 适配美团云网络架构的 Docker 网络驱动。在创建容器时，需要指定容器创建参数—net=mosbridge，并将 ip 地址，网关，OVS Bridge 等参数传给 Docker，由 MosBridge 完成网络的配置过程。有了 MosBridge，容器创建启动后便有了网络可以使用。容器的网络配置也持久化在 MosBridge 中，容器重启后网络配置也不会丢失。

### 容器存储隔离性

为了解决本地磁盘数据卷限容能力弱的问题，我们开发了 LVM Volume 方案。该方案在宿主机上创建一个 LVM VG 作为 Volume 的存储后端。创建容器时，从 VG 中创建一个 LV 当作一块磁盘，挂载到容器里。这样 Volume 的容量便由 LVM 的 LV 加以强限制，得益于 LVM 机强大的管理能力，我们可以做到对 Volume 更精细、更高效的管理。例如，我们可以很方便地调用 LVM 命令查看 Volume 使用量，通过打标签的方式实现 Volume 伪删除和回收站功能，还可以使用 LVM 命令对 Volume 做在线扩容。

(点击放大图像)

[![](https://cdn.kelu.org/blog/2017/11/meituan/4.jpg)](https://cdn.kelu.org/blog/2017/11/meituan/4.jpg)

图 3\. LVM-Volume 方案

### 容器状态采集

在设计实现容器监控之前，美团点评内部已经有了许多监控服务，例如 zabbix，falcon 和 CAT。我们不需要设计实现一套完整的监控服务，而更多地是如何高效地采集容器运行信息，根据运行环境的配置上报到相应的监控服务上。

(点击放大图像)

[![](https://cdn.kelu.org/blog/2017/11/meituan/5.jpg)](https://cdn.kelu.org/blog/2017/11/meituan/5.jpg)

图 4：监控数据采集方案

针对业务和运维的监控需求，我们基于 libcontainer 开发了 mos-docker-agent 监控模块。该模块从宿主机 proc、cgroup 等接口采集容器数据，经过加工换算，再通过不同的监控系统 driver 上报数据。该模块使用 go 语言编写，既可以高效率，又可以直接使用 libcontainer。而且监控不经过 Docker Daemon，不加重 Daemon 的负担。

## 美团自研容器分支：MosDocker

### 基本介绍

我们从 Docker 1.11 版本开始，自研维护一个分支，我们称之为 MosDocker。除了一些 Bugfix 之外，MosDocker 还有一些自研的特性。

1.  MosBridge：支持美团云网络架构的网络驱动。
2.  Cgroup 持久化：扩展 Docker Update 接口，可以使更多的 cgroup 配置持久化在容器中，保证容器重启后 cgroup 配置不丢失。
3.  支持子镜像的 Docker Save，可以大幅度提高 Docker 镜像的上传、下载速度。

MosDocker 的大部分特性是解决美团点评业务场景的需求问题，不适合开源。对于社区的 bugfix，我们会定期 review 并 backport 到我们的 MosDocker 版本里。

### 原理探究：服务治理与容器编排

(点击放大图像)

[![](https://cdn.kelu.org/blog/2017/11/meituan/6.jpg)](https://cdn.kelu.org/blog/2017/11/meituan/6.jpg)

图 5：美团点评服务治理平台

## 组件：

*   MNS：命名服务
*   SG_agent: 服务治理代理
*   HLB：弹性负载均衡器

## 功能：

*   服务命名、注册、发现
*   负载均衡
*   容错
*   灰度发布
*   服务调用数据可视化

(点击放大图像)

[![](https://cdn.kelu.org/blog/2017/11/meituan/7.jpg)](https://cdn.kelu.org/blog/2017/11/meituan/7.jpg)

图 6：set 容器组设计

## 设计特性

*   松耦合
*   容器组作为一个整体调度、管理
*   容器之间 CPU、IO 资源隔离
*   网络栈，Volume 共享

参考 Kubernetes 的 Pod 设计，针对我们的容器平台设计实现了 set 容器组。容器组对外呈现一个 IP，内置一个 busybox 作为 infra-container，它不提供任何业务功能，set 的网络、volume 都配置在 busybox 上，所有其他的容器都和 busybox 共享。

## 在实际业务中的推广应用

通过引入 Docker 技术，为业务部门带来诸多好处，典型的好处有几点：

*   **快速部署，快速应对业务突发流量**由于使用 Docker，业务的机器申请、部署、业务发布一步完成，业务扩容从原来的小时级缩减为秒级，极大地提高了业务的弹性能力。

*   **节省 IT 硬件和运维成本**Docker 在计算上效率更高，加之高弹性使得业务部门不必预留大量的资源，节省大量的硬件投资。

    以某业务为例，之前为了应对流量波动和突发流量，预留了 32 台 8 核 8G 的虚拟机。使用容器弹性方案，即 3 台容器 + 弹性扩容的方案取代固定 32 台虚拟机，平均单机 QPS 提升 85%， 平均资源占用率降低 44-56%。

    (点击放大图像)

    [![](https://cdn.kelu.org/blog/2017/11/meituan/8.jpg)](https://cdn.kelu.org/blog/2017/11/meituan/8.jpg)

    图 7\. 某业务虚拟机和容器平均单机 QPS

    (点击放大图像)

    [![](https://cdn.kelu.org/blog/2017/11/meituan/9.jpg)](https://cdn.kelu.org/blog/2017/11/meituan/9.jpg)

    图 8 某业务虚拟机和容器资源使用量

*   **Docker 在线扩容能力，保障服务不中断**

## 总结

在容器化过程中，我们发现 Docker 或者容器技术本身存在许多问题和不足，例如，Docker 存在 IO 隔离性不强的问题，无法对 Buffered IO 做限制；偶尔 Docker Daemon 会卡死，无反应的问题；容器内存 OOM 导致容器被删除，开启 OOM_kill_disabled 后可能导致宿主机内核崩溃等等问题。因此 Docker 或者容器技术，在我们看来应该和虚拟机是互补的关系，不能指望在所有场景中 Docker 都可以替代虚拟机，因此只有将 Docker 和虚拟机并重，才能满足用户的各种场景对云计算的需求。

## QA 环节

**Q1: 想问下，在美团点评内部的业务系统中，一般应用容器的规格大致是什么范围，3C 4G 这些？因为有些容器规格分配太大，对于弹性扩缩容，有些影响。导致某些节点存在大量的资源碎片。谢谢**

> **A1:**4G 内存的容器还好，我们线上物理机标配是 128G 内存，而且容器的内存只是 cgroup 里的一个设置，实际分配内存还是以容器使用为准。对于一些内存 bound 型业务，内存需要 10G 甚至更多的，我们给这类业务单独一个物理机集群使用，不和其他业务混部。

**Q2：k8s 的优势所在，Docker 在美团使用情况？**

> **A2：**kubernetes 源于 Google 的 Borg 系统，Borg 是一个分布式容器集群管理平台，在 Google 内部使用 10 多年，承载海量的 Google 线上业务，架构、性能都在业内有广泛声誉。Kubernetes 可以看作 Borg 用 Go 语言重写，和 Borg 一脉相承，所以 Kubernetes 一经推出便广受业界推崇，现在已经是容器集群管理系统的业界标杆。简单来说 Kuberenetes 有一下优势：
> 
> 1.  松耦合架构设计：etcd 是 k8s 的元数据存储中心，使用 RAFT 算法保证数据强一致性，除了支持 POST/PUT/GET/DELETE 之外，还支持 WATCH，是 k8s 的真实之源。apiserver 是无状态的 API 服务器，是唯一可以和 etcd 通信的组件，所有其他组件都是通过 apiserver 访问 etcd 的，apiserver 可以水平扩展。Controller-mananger 集成 RC、Deployment 等控制器。Scheduler 是 Pod 的调度器。Kubelet 是 Node 上资源和容器 runtime 的管理器。除 etcd, apiserver 之外，所有组件之间的通信方式都是通过 Watch etcd 交换数据，组件之间不直接通信，这种设计使整个架构彻底松耦合，任何组件都很容易被替换、扩展的。
>     
>     
> 2.  Pod 容器组支持：Pod 是一个容器组的抽象概念，将一组容器聚合成一个 Pod，作为调度和资源分配的接本单位。Pod 的设计更贴近生成场景对容器的需求，因为实际一个服务都是有多个业务进程组成的，这些进程在 VM 时代一般都放在同一个 VM 内，而在容器时代，最佳方案是一个容器只运行一个进程，而将这些强相关的进程放到不通的容器里，再将这些容器聚合成一个 Pod，是更理想的使用容器的方式。
>     
>     
> 3.  Kubernetes 不仅是一个物理机 /VM 集群管理系统，或者说不仅是一个 IaaS，它自带许多容器的编排控制功能，例如 RC，Service。容器时代业界更关心基于容器的编排管理，Kubernetes 考虑了大多数容器生成场景的需求，内置了许多容器编排功能，可以让用户开箱即用的方式使用容器自动化部署业务，高效运维。
>     
>     
> 4.  kubernetes 已经成为业界容器标准之一，社区火热，版本更新快，因此使用 k8s，在长期的技术支持上，是有比较长期的、稳定的报障。

**Q3：容器能否在生产环境实际应用，并保持稳定的性能、不影响业务系统？**

> **A3：**
> 
> 1.  从技术上说，容器技术有 10 几年的历史，Docker 也有 5，6 年的历史了，容器 /Docker 所依赖的 Linux 内核 Cgroup 和 Namespace 技术，历史更悠久，因此容器的技术可靠性是完全可以信赖的。
>     
>     
> 2.  Docker 版本更新较快，版本迭代频繁，而且 docker 没有长期稳定版 LTS 的支持，所以在生产场景中使用，难免会踩到 Docker 的 Bug，所以如果要在实际生产环境中大规模使用，需要有对 Docker 本身的维护能力。
>     
>     
> 3.  Docker 和 VM 相比，虚拟化层次不同，导致 Docker 的隔离性要比 VM 弱，在多租户环境中有安全隔离的问题，另外在单宿主机上，容器之间的资源竞争也可能导致业务会受到影响，需要对容器的资源隔离，特别是 cgroup 的隔离有较为精细的运维。

**Q4：volume 管理都做了哪些工作？特别想知道怎么管理数据的**

> **A4：**美团云 Docker 平台的 Volume 管理经过 3 个阶段：
> 
> 阶段 1：本地文件系统做 volume，优点是开发快，缺点是限容、隔离性差，数据可靠性差
> 
> 阶段 2：本地 lvm 磁盘，解决了 volume 隔离性问题，但可靠性仍然不足
> 
> 阶段 3：现阶段，我们使用美团云自研的分布式弹性块存储服务，相当于 aws 的 EBS，用 ebs 做 volume 存储后端，完全解决了容量限制，隔离性，可靠性的问题，在 volume 备份、迁移上也有优势。

**Q5：swarm 和 k8s、mesors 哪个更建议生产环境使用？**

> **A5：**据我所知，业界大规模使用容器的方案有 k8s 和 Mesos 的，Swarm 早期功能简单，不能满足生产所需，不过 Docker 公司对 Swarm 的更新很快，而且 Swarm 和 Docker 是一致的接口，相信以后会有较多的实践。

**Q6: 请问美团后端储存用的是普通的磁盘，还是储存阵列，如果是存储阵列，是如何集成管理的呢？**

> **A6：**美团云存储后端有分布式对象存储系统，也有分布式块存储系统。都是美团云自研的系统。

## 作者介绍

**郑坤**， 2011 年中科院计算所博士毕业，曾在华为从事内核研发工作。2015 年加入美团，负责美团云容器平台的设计开发，以及在美团点评内部容器化推广工作。很高兴本次分享 Docker 技术在美团点评的实践情况。

* * *

感谢[木环](http://www.infoq.com/cn/author/%E6%9C%A8%E7%8E%AF)对本文的审校。