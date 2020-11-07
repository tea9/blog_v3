---
title: android 权限提升
abbrlink: 863526240
date: 2019-08-01 10:08:55
tags:
  - android安全
  - app漏洞
  - android权限提升
categories:
  - - 安全
    - 移动安全
    - android安全
    - android漏洞
description: android权限提升
password: teanb
abstract: 这里有东西被加密了，需要输入密码查看哦。
message: 您好，这里需要密码。
wrong_pass_message: 抱歉，这个密码看着不太对，请再试试。
wrong_hash_message: 抱歉，这个文章不能被纠正，不过您还是能看看解密后的内容。
---
## 搜狗输入法的com.sogou.udp.push.PushServiceReceiver导出，并且数据可控，通过构造恶意数据，可以触发通知及振动响铃。

APP在不需要相关权限情况下会频繁触发手机震动、响铃功能，触发恶意通知信息，给用户造成安全隐患。
在正常状态下，如果一些恶意的APP借用这个广播，触发里面的广播机制，不需要加入手机震动权限，可造成手机频繁响铃震动或者弹窗风险等问题。

## service 