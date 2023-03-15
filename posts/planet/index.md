# Planet：一款IPFS静态站点生成器


对于不少人来说，搭建一个自己的网站是件不好入手的事情。抛开一系列眼花缭乱的技术术语，还需搞定服务器、域名、建站工具等等，这些都要气力去认知与实践。况且随着互联网寡头横行霸道，似乎发布或拥有一个网志站点已变为画蛇添足的笨蛋行为。

万幸，分布式网路虽英雄末路，但还未乘鹤西去，已有开发者将发布分布式网站做成了一件用户操作友好的事情。妳只需在macOS上安装Planet，就可将IPFS作为托管服务器，几下点击就可发布一个网志站点。

## Planet的缘起

Planet的创始人Livid也是大型程序员交友网站[v2ex](https://www.v2ex.com/)的创始人，在Planet发布前的[一档播客访谈](https://www.xiaoyuzhoufm.com/episode/626ba660bf39836fd02b78e9)
中，Livid称维护v2ex的服务器与数据库是一件漫长且痛苦的事情，并表示如果再开发一款程序，自己不会选择再托管用户的数据。

>星际文件系统（Interplanetary File System，缩写为IPFS）是一个旨在实现文件的分布式存储、共享和持久化的网络传输协议。[2]它是一种内容可寻址（英语：Content-addressable storage）的对等超媒体分发协议。在IPFS网络中的节点构成一个分布式文件系统。
>
>——[维基百科「星际文件系统」词条](https://zh.m.wikipedia.org/zh-hans/%E6%98%9F%E9%99%85%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F)

IPFS作为分布式网路技术，并且可实现用户自己托管自己的数据，这让Livid看到了光亮，遂有了开发Planet的想法。西元2022年07月11日，Livid在v2ex[发帖](https://www.v2ex.com/t/857404)，「如果你之前用过 IPFS 或者 ENS，你可能会想要试试我们在做的这个App」，Planet正式上线。

Livid在访谈中表示，底层协议的迭代已经成熟，分布式站点完全可能，Planet会做一个用户友好的进入分布式的入口。

## 站点的访问

在开始使用Planet部署自己的分布式站点之前，我们有必要有了解一下访问一个万维网站点的过程。

{{< figure src="/images/planet/2.png" title="浏览器访问万维网过程的简易示意图">}}

在访问传统万维网站点的过程中，首先浏览器要跟DNS服务器请求服务器地址，我们可以把这个过程想象成在手机地图中输入商家名称，获得商家地址；然后，有服务器地址，浏览器就可以与服务器建立连接，我们可以把这个过程想象成有商家地址，我们就可以乘坐共同交通前往与返回；最后，当建立了连接，客户浏览器就可以发送需要的文件，服务器收到了这个请求，就会将相应的文件返回给浏览器，浏览器得到文件就可以将内容显示给用户，我们可以将这个过程想象成我们在外送app上点了一个红烧肉，商家收到了订单，做好后就发送给用户。

{{< figure src="/images/planet/3.jpg" title="世界上第一台服务器">}}

也许有人会问，什么是服务器呢？事实上，服务器就是一台电脑，任何联网的设备都可以成为一台服务器，妳的手机或电脑都可以作为服务器供她人访问。被提姆·柏内兹-李在CERN所使用的这台NeXT计算机成为了世界上第一台网页服务器，上面写着「这台机器是服务器，不要关闭电源」。

然而，时至今日，互联网寡头大行其道，加之国内糟糕的联网环境，已经很少人在家中自行搭建站点服务器了。我们平时所访问站点更多是托管在云服务器商（阿里云、亚马逊云等等）之上，这与万维网的理念完全背离，回滚到电台广播的时代。「偷听敌台」，「私建电台」，已然成为抗命行为。

那么，Planet生成的IPFS站点有什么不同？

{{< figure src="/images/planet/4.png" title="浏览器通过ENS域名访问IPFS站点的简易示意图">}}

我们可以简单地认为IPNS代替了传统域名和DNS服务器，IPFS代替了服务器。

一个网站主要使用由域名与服务器构成，与传统站点不同，IPFS的域名与服务器可完全实现分布式。IPNS是IPFS生态中的一个重要组件，全称是 Interplanetary Name System，一个去中心化的类似域名的系统。IPNS可绑定在一些去中心域名，也可绑定在传统域名域名中。而由于IPFS是一个分布式文件存储系统，所以使用IPFS作为站点服务器，也就实现了服务器的分布式。

## 开始使用

{{< figure src="/images/planet/5.png" title="点击「Download for Mac」下载">}}

Planet是一个开源的macOS原生App，我们通过它的[官網](https://www.planetable.xyz/)与[GitHub](https://github.com/Planetable/Planet)进行下载。

下载安装完成后，打开Planet，它会自动follow这两个站点：

- vitalik.eth - Ethereum 的创始人之一
- Planetable.eth - Planet 项目的博客

{{< figure src="/images/planet/6.png" title="点击主界面下方的加号">}}

{{< figure src="/images/planet/7.png" title="写入名字与简介，选择模版，创建自己的Planet">}}

{{< figure src="/images/planet/8.png" title="点击撰写按钮">}}

{{< figure src="/images/planet/9.png" title="写入网志内容，并点击发布">}}

发布完第一个篇文章之后，妳的 Planet 就会被发布为一个 IPNS。右键点击侧栏里你的站点，选择 Copy IPNS。然后你就会在剪贴板中获得像这样的一串东西：

{{< figure src="/images/planet/10.png" title="Copy IPNS">}}

k51qzi5uqu5dhxd50115dn1hfvuwiqwej3dki72uyopetqua71i6lp96pem0a6

然后妳把这串IPNS发给其它Planet用户，她们就可以收到来自妳的更新了。

妳用Planet发布的网站，也可能可以通过各种 Public Gateway 访问，比如这是你当前正在阅读的这篇文章在各个 Gateway 上的地址（URL 拼接规则是 Public Gateway 域名 + /ipns/ + Planet.ipns + / + Article.UUID）：

- ipfs.io
- dweb.link
- cf-ipfs.com

## 域名绑定

### 传统域名绑定

绑定传统域名，需要使用的是[DNSlink](https://docs.ipfs.tech/concepts/dnslink/)服务，大概的意思就是在DNS上写入txt记录，以供读取。因为使用到传统的DNS服务，所以IPNS关联到传统域名，并非是一个去中心的解决方案。

{{< figure src="/images/planet/11.png">}}

例如，绑定Planet到sogola.com主域名，

IPNS的地址：

k51qzi5uqu5dhxd50115dn1hfvuwiqwej3dki72uyopetqua71i6lp96pem0a6

则到域名DNS管理页上创建一个txt记录

设置name值为：

- _dnslink

设置Content值为：

- dnslink=/ipns/k51qzi5uqu5dhxd50115dn1hfvuwiqwej3dki72uyopetqua71i6lp96pem0a6

{{< figure src="/images/planet/12.png">}}

例如，绑定Planet到www.sogola.com二级域名，

设置name值为：

- _dnslink.www

设置Content值为：

- dnslink=/ipns/k51qzi5uqu5dhxd50115dn1hfvuwiqwej3dki72uyopetqua71i6lp96pem0a6

{{< figure src="/images/planet/13.png">}}

等待DNS生效，使用原生支持IPFS的Brave浏览器就可以直接访问 ipns://sogola.com/ 。

{{< figure src="/images/planet/14.png">}}

如果在DNS中添加cloudflare的IPFS gateway，也可以使用普通浏览器的访问。

### ENS绑定

使用ENS绑定IPNS，就是利用Ethereum账本作为DNS账本。在ENS合约之中，将IPNS地址记录到Content Hash，这样任何人都可以访问Ethereum账本，查询相应的ENS绑定的IPNS。

因为设定Content Hash会是一个 ENS 合约上的操作，所以这一步会有gas fee。但是之后妳在 Planet里发布新的内容，妳的IPNS不会发生改变，也不会再有gas fee的问题。

{{< figure src="/images/planet/15.png">}}

首先，打开ENS管理页面 https://app.ens.domains/

ipns://k51qzi5uqu5dhxd50115dn1hfvuwiqwej3dki72uyopetqua71i6lp96pem0a6

然后，将IPNS写入Content Hash，并确定上链。

{{< figure src="/images/planet/16.png">}}

这里有一个小tip，如果妳并不着急解析立刻失效，可自定义Max base fee和Priority fee。在metamask中打开gas fee的高级管理选项，在确认操作的时候，编辑gas fee选项即可。

在完成了ENS绑定之后，也可以直接用类似下面这样的地址通过Public Gateway打开妳的 ENS：

- https://ipfs.io/ipns/sogola.eth
- https://sogola.eth.limo

{{< figure src="/images/planet/17.png">}}

在原生支持IPFS的Brave浏览器里，你甚至可以用 ipns://sogola.eth 这样的地址直接打开妳的IPFS站点。

## 关于Planet的疑问

>阅读器用自己的格式渲染原文，可以让体验更一致，及对离线阅读更友好。但对于网站的原作者，会丢失原始网站的设计及影响源网站的利益。
>
>—— Livid在Planet中的发言

使用Planet订阅Planet、IPFS站点或RSS，会自动加载站点的原生样式，这样的做法无疑是在开发一款浏览器，而非阅读器。对于读者用户来讲，这不是一个好的体验，比较kindle这样的电纸书也不会不允许读者自定义图书的样式。至于创作者的收益与读者的便利，我想肯定应该会有一个平衡点，但将阅读器重新发明为浏览器，无疑不是一种好的选择。

因为IPFS的特性，Planet站点使用普通域名往往访问过慢，这时候每个用户要尽可能地获得更多follower。

对于这一点，Livid在用户聊天群中表示：将来可以成立一个数据DAO来提供托管服务，但目前的主要工作还是完善软件、扩大用户数量。

Planet目前只支援macOS，至于使用更为普遍的Windows，开发者好像并没有支援计划。桌面操作系统的份额，Windows是最大，分布式网路依靠用户数量壮大才能取胜，只支援macOS与扩大节点数量是存在矛盾的，我们只能寄希望于有其它开发者会开发一款兼容Windows的客户端。

如果使用过程中遇到什么问题，可进入Planet的中文用户群，Livid是一位热心的开发者，他很乐意与用户交流。

https://t.me/+5bl7FIsxeChlOWIz

最后，衷心地祝福Planet做强做大，分布式永垂不朽。

## 参考资料

- Livid的Planet https://olivida.eth.limo
