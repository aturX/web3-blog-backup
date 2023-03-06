---
title: "如何采用 Web3全栈解决方案 Fleek ?"
datePublished: Fri Mar 03 2023 13:57:57 GMT+0000 (Coordinated Universal Time)
cuid: clesloqbr000l09l86vqo3e7c
slug: web3-fleek
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1678110150946/6086fe7e-b7ff-45dd-a8d5-db121d860bcf.png

---

Fleek 是一个 Web3基础设施平台，可提供 IPFS、Arweave、Filecoin、Akask、POKT、ENS 等协议。单独使用它们或将它们组合在一起以创造令人惊叹的新体验。

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677827003636/e12d06e6-d2a5-499a-90cd-144a2e16adba.png align="center")

Web3 不会是一个单一的堆栈世界。Fleek 的目标是为 web3 基础架构提供支持（托管、存储、网关、域、CDN、计算等）的所有可能组合和排列提供公正的可访问性，而无需你自己花时间深入研究集成它们的细微差别和复杂性。你将你的应用程序连接到 Fleek，Fleek将你连接到你首选的 web3 基础设施。

![建筑学](https://docs.fleek.xyz/_next/static/media/xyz-arch-services.7b2ca9ee.png align="left")

Fleek 采用了完整的微服务架构，已经分解成更小的部分，因此你可以独立使用它们，相互组合或使用自己的组合，这些组合已经针对最需要的用例进行了优化。

所有的微服务都将被一些服务所遍历，这些服务将成为所有服务的标准，例如身份验证、计费等等，这将使与 Fleek 的交互尽可能简单。

# 关于 [fleek.co](https://fleek.co/) 与 [fleek.xyz](https://fleek.xyz/)

![](https://storageapi.fleek.co/fleek-team-bucket/thumbnails/xyz-ntwrk-release-image.png align="left")

Fleek 在 2022 年结束时发布三项重大公告 ：

1. **他们刚刚完成了由 Polychain 和去中心化存储生态系统的主要支持者领投的 2500 万美元的 A 轮融资。**
    
2. **他们宣布 Fleek Network 将修补 web3 基础架构的缺失部分——内容交付。**
    
3. **他们宣布一个全新的 Fleek 平台将成为 web3 的开发平台——协议不可知、非托管和开源。**
    

用户可以如何参与:

* [在Fleek.xyz](https://fleek.xyz/) 上注册以获取更新。
    
* [在Fleek Network](https://fleek.network/) 上注册以获取更新。
    
* 加入[Fleek Discord](https://discord.gg/fleekxyz)。
    
* 阅读 [Fleek Network 白皮书。](https://fleek.network/fleek-network.pdf?202212011428)
    
* [测试运行](https://blog.fleek.co/posts/fleek-network-getting-started-guide)Fleek Network 的节点。 (重点)
    

# Fleek的优势

Fleek Network 是一个**闪电般快速的去中心化内容和应用程序交付网络，它将加速所有 web3 内容和应用程序的交付**。作为一个无需信任、抗审查、开源的 CDN，任何人都可以通过运行缓存节点来为网络贡献带宽。

Fleek Network 与来源无关，这意味着它可以加速来自任何底层存储协议或提供商的内容，并且网络的使用将完全透明并公开供全世界使用。所有内容都将基于 IPLD 且内容可寻址，创建全球带宽和所服务内容的公共记录，就像智能​​合约如何为金融交易带来彻底的透明度一样。

Fleek Network 的技术架构非常稳定, 已经经历过2年的完善，并且**已经在测试中看到了令人印象深刻的性能指标（例如 15-20 毫秒 TTFB）**。从成本、性能/延迟和吞吐量的角度来看，Fleek Network 将与现有的 CDN 一样好，甚至更好，同时保持 Fleek Network 与其他 CDN 显着区别的去中心化/web3 优势。

大多数流行的 web3 应用程序和用例（以及存储协议/服务提供商）都使用 Cloudflare 来加速他们的应用程序和内容。这是因为最重要的是，**用户不会容忍缓慢加载应用程序/内容。**

然而，**问题是 web3 协议不会激励**直接从他们的网络进行内容的高性能交付，所以每个人都被迫在所有这些协议和他们的应用程序之间坚持使用高性能缓存层（例如 Cloudflare）。

解决方案？Fleek Network，**专为任何想要快速且无需信任的内容加速的人而构建**。

**总结: Vercel + Cloudflare + Web3 = Fleek**

# 几分钟内使用 Fleek 发布一个Dapp

Fleek 官方提供了一下Demo, 我们直接来通过Fleek部署发布一个 Dapp 网站到IPFS上.

Demo Github: [https://github.com/fleekxyz/fleek-demos-blog](https://github.com/fleekxyz/fleek-demos-blog)

登陆: [https://app.fleek.co/](https://app.fleek.co/)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677829497848/4578eaaa-d8e7-4749-801d-9b84e1117999.png align="center")

在GitHub仓库上, 将Demo项目Fork了, 然后去登陆 Fleek 平台 , 在平台上连接 Github .

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677829737947/6804e586-c18e-4a4d-bcd9-900397e21152.png align="center")

选择好自己刚才的仓库, 直接部署就完成了 ! 就是这么简单快速 !

# 运行一个 Fleek Network 节点

在 Linux 服务器（例如 Ubuntu 或 Debian）中安装网络节点，以下是一个辅助安装程序，可帮助你尽快上手。

打开终端并执行：

(采用 Docker 来安装)

前置要求:

* Docker: [https://www.docker.com/](https://www.docker.com/) (脚本会帮忙安装)
    
* Linux
    
* 安装脚本 👇
    

```plaintext
curl https://get.fleek.network | bash
```

进入目录, 并切换的指定的代码版本:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677850145261/d99fbd9f-65f4-49e0-8b81-6bf0dec3b865.png align="center")

选择语言中文后, 一路回复 “是的” :

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677850412586/8a695a7f-378c-4c65-9b92-c6c79d166562.png align="center")

配置好邮件, 脚本会帮助我们注册申请 HTTPS 证书.

接着会进行Docker 镜像的拉取. 如下所展示, 将全部节点镜像加载完成.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677850578809/7cda813e-0a49-4d71-a662-f72dfb09324e.png align="center")

正常启动节点后, 会查看到这样的运行日志 :

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1677850759129/7c59e021-d9af-4ece-81e7-b9f31e47bde8.png align="center")

测试节点是否正常, 返回 `Pong`则正常:

```plaintext
curl ping https://你的域名.com/ping
```

最后注意, 节点的标识符是你的服务的私钥 (它证明了是你在运行节点) :

注意备份它, 它保存的位置在:

```plaintext
cd ~/.ursa/keystore

cat default.pem
```

## ***更多优质 Web3内容***

* [**订阅Youtube**](https://www.youtube.com/channel/UCDrmDcLjnmIQk-xtNuJ42Sw)
    
* [**关注Twitter**](https://twitter.com/AntCaveClub)