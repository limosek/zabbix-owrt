# Openwrt repository feed for zabbix packages

Zabbix and Zaf OpenWrt packages feed. You can use this feed to compile ucified zabbix packages with enhanced support and zaf included. Note this is not place for binary packages. You cannot use this feed directly in your router. This feed is used for compilation process. Binary packages will be (maybe) distributed later. But our goal is to include this repo or packages directly into openwrt packages feed.

## What is Zaf?

Zaf is Zabbix agent framework. It is used to automatize scripting of external Items for Zabbix and even more. See more at https://github.com/limosek/zaf/tree/1.3 
This repo integrates zaf into zabbix agent init scripts so zabbix is ucified and even more, you can use zaf plugins.

## How to use

First, you have to setup and configure your OpenWrt build dir. More info here: https://wiki.openwrt.org/doc/howto/build
Next, add this repo into feeds:
```
touch feeds.conf
echo src-git zabbix https://github.com/limosek/zabbix-owrt.git >>feeds.conf
./scripts/feeds update -a
./scripts/feeds install zabbix3 zaf
make menuconfig
```

Do not forget to disable zabbix packages and enable zabbix3 packages!

## Fine tuning ##

You can find zabbix configuration under Administration/zabbix3 un menuconfig. Zabbix is more configurable than its previous openwrt version. You can enable/disable more options like SSL or IPV6 support. See menuconfig. Even more you can make your zabbix packages preconfigured for your system (see Zaf options in menuconfig).

You can use all of configuration options from Zaf for your packages. For example:
```
ZAF_PLUGINS=zaf,iwx
ZAF_OPTIONS="Z_Server=zabbix.server.local Z_ServerActive=zabbix.server.local Z_HostnameItem=system.hostname Z_RefreshActiveChecks=60"
```


