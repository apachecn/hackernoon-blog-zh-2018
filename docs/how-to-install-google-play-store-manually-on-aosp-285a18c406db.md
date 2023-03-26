# 如何在 AOSP 上手动安装谷歌 Play 商店

> 原文：<https://medium.com/hackernoon/how-to-install-google-play-store-manually-on-aosp-285a18c406db>

## 谷歌 Play 商店在 AOSP

![](img/ce52f23915f4606fb25092a7badd1880.png)

Image via androidauthority.com

*注意:我测试的 AOSP 版本是 4.2.2 和 4.4.2。*

在`PackageManagerService.java`的`ingrantSignaturePermissionmethod`中的第 5634 行之后添加以下代码片段:

```
if (pkg.packageName.equals("com.android.vending") && compareSignatures(pkg.mSignatures, new…
```