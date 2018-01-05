> * 原文地址：[Double Stuffed Security in Android Oreo](https://android-developers.googleblog.com/2017/12/double-stuffed-security-in-android-oreo.html)
> * 原文作者：[Gian G Spicuzza](https://android-developers.googleblog.com/2017/12/double-stuffed-security-in-android-oreo.html)
> * 译文出自：[掘金翻译计划](https://github.com/xitu/gold-miner)
> * 本文永久链接：[https://github.com/xitu/gold-miner/blob/master/TODO/double-stuffed-security-in-android-oreo.md](https://github.com/xitu/gold-miner/blob/master/TODO/double-stuffed-security-in-android-oreo.md)
> * 译者：一只胖蜗牛
> * 校对者：

# [Android Oreo中的双塞安全](https://android-developers.googleblog.com/2017/12/double-stuffed-security-in-android-oreo.html)

由Gian G Spicuzza发表,Android安全团队

Android Oreo新增了许多安全验证功能.几个月以来,我们讨论了如何增强Android平台及应用的安全性: 从[更安全的获取应用](https://android-developers.googleblog.com/2017/08/making-it-safer-to-get-apps-on-android-o.html),减少[网络挟持](https://android-developers.googleblog.com/2017/04/android-o-to-drop-insecure-tls-version.html),提供更多[用户控制符](https://android-developers.googleblog.com/2017/04/changes-to-device-identifiers-in.html),[加固内核](https://android-developers.googleblog.com/2017/08/hardening-kernel-in-android-oreo.html),[使Android更易于升级](https://android-developers.googleblog.com/2017/07/shut-hal-up.html),直到[doubling the Android Security Rewards payouts](https://android-developers.googleblog.com/2017/06/2017-android-security-rewards.html).如今Oreo正式和大家见面了,让我们回顾下这其中的改进.  

### 扩大硬件安全支持

Android早已支持[开机验证模式(Verified Boot)](https://source.android.com/security/verifiedboot/),旨在防止设备启动软件被篡改.在Android Oreo中,我们随着[Project Treble](https://source.android.com/devices/architecture/treble)添加了一个实现了开机验证模式(Verified Boot)的实例,称之为Android开机验证模式2.0(Android Verified Boot 2.0)(AVB). AVB有一些很酷的功能使得更新更加容易,更加安全,例如通用页脚格式以及回滚保护.回滚保护旨在保护设备OS降级到低版本的系统后可能被人利用.为此,设备通过专用硬件及可信执行环境(Trusted Execution Environment)(TEE)签名的数据来保存OS版本.Pixel2和Pixel2XL有这种保护,并且我们建议所有设备制造商将这个功能添加到他们的新设备.

Oreo还包括新的[原始设备制造商(OEM)锁定硬件抽象层](https://android-review.googlesource.com/#/c/platform/hardware/interfaces/+/527086/-1..1/oemlock/1.0/IOemLock.hal)(HAL)使得设备制造商能够更加灵活的保护设备锁定、解锁或者可解锁性.例如,新的Pixel手机使用硬件抽象层(HAL)通过命令来引导装载程序.引导装载程序会在下次开机分析这些命令来确定是否更改锁,这个锁被安全的存储在重放保护内存快(RPMB).如果你的设备被偷了,这些保护措施旨在保护你的设备被重制从而保护你的数据安全.新的硬件抽象层(HAL)甚至支持将锁移动到专用的硬件中.

谈到硬件,我们添加了防伪硬件支持,例如在每一个Piexl2和Piexl2 XL中内嵌的[安全模块](https://android-developers.googleblog.com/2017/11/how-pixel-2s-security-module-delivers.html).这种物理芯片防止很多软硬件攻击,并且还抵抗物理渗透攻击. 安全薄块防止推导设备密码及限制解锁尝试的次数,使得很多攻击由于时间限制而失效.

而新的Pixel设备有特殊的安全模块,所有搭载Android Oreo的[谷歌移动服务(GMS)](https://www.android.com/gms/)设备需要实现[key验证](https://android-developers.googleblog.com/2017/09/keystore-key-attestation.html).这提供了一种强[验证IDs](https://source.android.com/security/keystore/attestation#id-attestation)机制,例如硬件标示.

我们也为企业设备管理添加了新的功能.加密密钥会被移除RAM当移除配置文件或者公司管理员远程锁定配置文件.这有助于企业数据的安全.

### 平台增强及进程隔离

作为[Project Treble](https://android-developers.googleblog.com/2017/05/here-comes-treble-modular-base-for.html)的一部分,Android架构重构为了使设备厂商更容易花费较小的代价进行升级.这种分离的平台和供应商代码也是为了提高安全性.根据[最小特权原则](https://en.wikipedia.org/wiki/Principle_of_least_privilege),这些硬件抽象层(HALs)运行在[自己的沙盒](https://android-developers.googleblog.com/2017/07/shut-hal-up.html),只有访问驱动和权限才是必须的.

追随着Android牛轧糖中[媒体堆栈优化](https://android-developers.googleblog.com/2016/05/hardening-media-stack.html)脚步,在Android Oreeo中移除了媒体框架中许多直接访问硬件的模块,从而分离开了这两层.此外,我们确保了在所有媒体组建中控制流完整性(CFI).许多通过破坏应用的正常控制流的缺陷如今被开发利用,从而利用应用程序的特权来执行恶意的活动. CFI是一个健全的安全验证机制,不允许随意更改原来编译后二进制文件的控制流程图,从而是的难以执行这样的攻击.

除了这些架构改变和CFI以外,Android Oreo还带来了其他美味的平台安全增强的盛宴:

* **[安全设计模式过滤](https://android-developers.googleblog.com/2017/07/seccomp-filter-in-android-o.html)**: 一些系统层的调用对应用不再开放,从而减少潜在损害应用途径.
* **[增强用户空间拷贝](https://lwn.net/Articles/695991/)**: 一个最新的关于Android相关的[安全漏洞掉渣](https://events.linuxfoundation.org/sites/events/files/slides/Android-%20protecting%20the%20kernel.pdf)显示在内核漏洞中失效的或者无边界检查占有约45%.在Android内核3.18及以上版本中我们添加了一个边界检查的补丁,这能够减少对这些漏洞的利用同时还同帮助开发者在他们代码中查找问题并修复bug.
* **Privileged Access Never(PAN)仿真**: 还针对3.18以上的内核打了补丁,这个功能禁止内核直接链接用户空间同时确保开发者利用增强的功能访问用户空间.
* **内核地址空间布局随机化(KASLR)**:虽然Android已经支持内核地址空间布局随机化(KASLR)好多年了 Although Android has supported userspace Address Space Layout Randomization (ASLR) for years,我们针对Android内核4.4以后版本新增了内核地址空间布局随机化(KASLR)补丁来帮助减少风险.内核地址空间布局随机化(KASLR)通过每次启动设备加载内核代码时随机分配地址来工作,使得代码复用攻击更加难以执行,尤其是远程攻击.

### 应用程序安全性及设备标示变更

[Android即时运行应用](https://developer.android.com/topic/instant-apps/index.html)运行在一个受限制的沙盒中从而限制了权限和功能,例如访问设备内应用或者明文传递数据.虽然是从Android Oreo才发布,但是即时运行应用支持在[Android Lollipop](https://www.android.com/versions/lollipop-5-0/)以后是设备上运行.

为了更安全的处理不可信内容,我们[隔离WebView](https://android-developers.googleblog.com/2017/06/whats-new-in-webview-security.html)通过将渲染引擎隔离到一个单独的进程并且将它运行在一个单独的沙河中从而限制它的资源开销. WebView还支持[安全浏览](https://safebrowsing.google.com/)从而预防潜在的危险网站.

最后,我们针对[设备标识做了重大的改变](https://android-developers.googleblog.com/2017/04/changes-to-device-identifiers-in.html)给了用户更多的控制权,包括:

* 移动静态的_Android ID_和_Widevine_值到一个应用特殊的值中,这帮助限制设备中不可重制IDs的使用.
* 依照[IETF RFC 7844](https://tools.ietf.org/html/rfc7844#section-3.7)匿名摘要 profile, `net.hostname` is now empty and the DHCP client no longer sends a hostname.
* 对于应用需要设备的ID,我们新增了一个`Build.getSerial() API`并且通过一个权限保护
* 与安全研究人员一起<sup>1</sup>,我们针对Wi-Fi扫描在各种芯片组中设计了一个健壮的MAC地址随机化功能e.

Android Oreo带来远不止这些改进,还有[更多](https://www.android.com/versions/oreo-8-0/).一如既往,如果您有关于Android提升的建议,我们欣然接受.请发送至security@android.com.

---

1:Glenn Wilkinson及新加坡的团队,UK, Célestin Matte, Mathieu Cunche:里昂大学,里昂国立应用科学学, CITI实验室, Mathy Vanhoef, KU Leuven

---

> [掘金翻译计划](https://github.com/xitu/gold-miner) 是一个翻译优质互联网技术文章的社区，文章来源为 [掘金](https://juejin.im) 上的英文分享文章。内容覆盖 [Android](https://github.com/xitu/gold-miner#android)、[iOS](https://github.com/xitu/gold-miner#ios)、[前端](https://github.com/xitu/gold-miner#前端)、[后端](https://github.com/xitu/gold-miner#后端)、[区块链](https://github.com/xitu/gold-miner#区块链)、[产品](https://github.com/xitu/gold-miner#产品)、[设计](https://github.com/xitu/gold-miner#设计)、[人工智能](https://github.com/xitu/gold-miner#人工智能)等领域，想要查看更多优质译文请持续关注 [掘金翻译计划](https://github.com/xitu/gold-miner)、[官方微博](http://weibo.com/juejinfanyi)、[知乎专栏](https://zhuanlan.zhihu.com/juejinfanyi)。