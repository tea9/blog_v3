---
title: 题目1-安卓手机文件分析取证(Wi-Fi名称)
tags:
  - 题目
  - 墨者学院
category:
  - 题目
abbrlink: 2060635638
date: 2020-08-12 11:23:31
description: 安卓手机文件分析取证、wpa_supplicant.conf分析
---

墨者学院一道题目-安卓手机文件分析取证(Wi-Fi名称)  
[题目链接](https://www.mozhe.cn/bug/detail/K1doaXNFemlxODh1QUQ1WVJyOTZRQT09bW96aGUmozhe)  

下载解压

找到wpa_supplicant.conf配置文件，记录着WIFI的信息，找到ssid  

```
find . -name "wpa_supplicant*"
open ./mnt/shell/emulated/0/data/wpa_supplicant.conf
填入123在验证的网站上之后会弹出flag

```

wpa_supplicant.conf  
```
ctrl_interface=/data/misc/wifi/sockets
disable_scan_offload=1
driver_param=use_p2p_group_interface=1
update_config=1
device_name=2014813
manufacturer=Xiaomi
model_name=2014813
model_number=2014813
serial_number=47e4efd4
device_type=10-0050F204-5
config_methods=physical_display virtual_push_button
p2p_disabled=1
p2p_no_group_iface=1network={
	ssid="123"
	psk="714825219"
	key_mgmt=WPA-PSK
}

```
获取flag  
验证通过, Key is: mozheffd83ed0bafcc67a991a7b6ac14  

![](https://raw.githubusercontent.com/tea9/image/master/blog_img/34/01.png)

## 小结

wpa_supplicant.conf 配置文件解析（一）  


WPA，是Wi-Fi Protected Access，Wi-Fi安全访问的简称。wpa_supplicant是开源项目源码，被谷歌修改后加入android移动平台，它主要是用来支持WEP，WPA/WPA2和WAPI无线协议和加密认证的。  

WPA_Supplicant功能：通过socket（不管是wpa_supplicant与上层还是wpa_supplicant与驱动都采用socket通讯）与驱动交互上报数据给用户，而用户可以通过socket发送命令给wpa_supplicant调动驱动来对WiFi芯片操作。简单的说，wpa_supplicant就是wifi驱动和用户（wifi应用程序）的中间件，支持相关协议和加密认证。  

```
wpa_supplicant.conf配置文件解析：

update_config=1      //是否允许wpa_supplicant更新（覆盖）配置
eapol_version=1  //IEEE 802.1X / EAPOL版本
ap_scan=1  //AP扫描/选择
passive_scan=0   //是否强制被动扫描进行网络连接
user_mpm=1   //MPM驻留
max_peer_links=99  //最大对等链路数（0-255;默认值：99）
mesh_max_inactivity=300   //检测STA不活动的超时（以秒为单位）（默认值：300秒）
cert_in_cb=1  //cert_in_cb - 是否在事件中包含对等证书转储
fast_reauth=1   //EAP快速重新认证
driver_param="field=value"   //驱动程序接口参数
country=US  //国家码
dot11RSNAConfigSATimeout=60  //PMKSA的最长寿命，以秒为单位; 默认43200
uuid=12345678-9abc-def0-1234-56789abcdef0  //设备的通用唯一标识符
auto_uuid=0   //自动UUID行为
device_name=Wireless Client  //设备名称
manufacturer=Company  //生产厂家
model_name=cmodel  //型号名称
model_number=123 // 型号
serial_number=12345  //序列号
device_type=1-0050F204-1  //主要设备类型
os_version=01020300    //操作系统版本
config_methods=label virtual_display virtual_push_button keypad  //配置方法
wps_cred_processing=0   //凭证处理
wps_vendor_ext_m1=000137100100020001  //WPS M1中的供应商属性，例如，Windows 7垂直配对
wps_nfc_dev_pw: Hexdump of Device Password     //WPS的NFC密码令牌
wps_priority=0  //通过WPS添加网络的优先级
bss_max_count=200   //要保留在内存中的最大BSS条目数
filter_ssids=0  //filter_ssids - 基于SSID的扫描结果过滤
p2p_disabled=1   //禁用P2P功能
p2p_go_max_inactivity=300  //检测STA不活动的超时（以秒为单位）（默认值：300秒）
p2p_passphrase_len=8   //P2P GO的密码长度
p2p_search_delay=500   //并发P2P搜索迭代之间的额外延迟
okc=0   //机会密钥缓存（也称为主动密钥缓存）默认
pmf=0  //受保护的管理框架
sae_groups=21 20 19 26 25  //按优先顺序启用SAE有限循环组
dtim_period=2   //DTIM周期的默认值（如果未在网络块中覆盖）
beacon_int=100   //Beacon间隔的默认值（如果未在网络块中覆盖）
ap_vendor_elements=dd0411223301   //Beacon和Probe Response帧的其他供应商特定元素
ignore_old_scan_res=0  //忽略比请求更早的扫描结果
mac_addr=0  //MAC地址策略
rand_addr_lifetime=60  //随机MAC地址的生命周期，以秒为单位（默认值：60）
preassoc_mac_addr=0   //预关联操作的MAC地址策略（扫描，ANQP）
gas_rand_mac_addr=0  //GAS操作的MAC地址策略
gas_rand_addr_lifetime=60   //GAS随机MAC地址的生命周期（以秒为单位）
interworking=1   //启用互通
go_interworking=1   //启用互通的P2P GO广告
go_access_network_type=0   //P2P GO互通：接入网络类型
go_internet=1   //P2P GO互通：网络是否提供到Internet的连接
go_venue_group=7  go_venue_type=1  //p2p-go互通：群组场馆信息（可选）
hessid=00:11:22:33:44:55  //同源ESS标识符
auto_interworking=0   //自动网络选择行为
gas_address3=0  //GAS Address3字段行为
ftm_responder=0  // 在扩展功能元素位70中发布精确定时测量（FTM）响应器功能。
ftm_initiator=0  //在扩展功能元素位71中发布精确定时测量（FTM）启动器功能。
mbo_cell_capa=3  //MBO蜂窝数据功能
non_pref_chan=81:5:10:2 81:1:0:2 81:9:0:2  //多频段操作（MBO）非首选频道
 oce=1       //优化的连接体验（OCE）
mem_only_psk=0        //mem_only_psk：是否仅在内存中保留PSK /密码
```