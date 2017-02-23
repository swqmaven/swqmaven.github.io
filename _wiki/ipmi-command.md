---
layout: wiki
title: ipmitools command
categories: [ipmi,linux]
description: ipmi 常用命令操作。
keywords: 'ipmi'
---

### ipmitools常用命令

## chassis
| 功能                      | 命令                                |
|:--------------------------|:--------------------------------------|
| pxe引导                   | ipmitool -I lanplus -H 172.16.0.11 -U ADMIN -P ADMIN chassis bootdev pxe|
| disk引导                  | ipmitool -I lanplus -H 172.16.0.11 -U ADMIN -P ADMIN chassis bootdev disk                          |
| cdrom引导                  | ipmitool -I lanplus -H 172.16.0.11 -U ADMIN -P ADMIN chassis bootdev cdrom                          |
| 关闭电源                  | ipmitool -I lanplus -H 172.16.0.11 -U ADMIN -P ADMIN chassis power off                          |
| 启动电源                  | ipmitool -I lanplus -H 172.16.0.11 -U ADMIN -P ADMIN chassis power on                          |
| 强制重启                  | ipmitool -I lanplus -H 172.16.0.11 -U ADMIN -P ADMIN chassis power reset                          |
| 电源状态                  | ipmitool -I lanplus -H 172.16.0.11 -U ADMIN -P ADMIN chassis power status                          |
