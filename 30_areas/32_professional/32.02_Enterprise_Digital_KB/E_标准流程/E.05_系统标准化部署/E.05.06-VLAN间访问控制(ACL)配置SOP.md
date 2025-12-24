# E.05.06-VLAN间访问控制(ACL)配置SOP

> **标签**: `#SOP` `#ACL` `#网络安全` `#VLAN` `#零信任`
> **版本**: 13.0 (终极完整版)
> **状态**: 最终版
> **关联设计**: [[A.01-IP与VLAN分配-总表]]

---

## 1. 概述与架构原则

本SOP定义了在**核心交换机**上配置ACL的最终流程，其唯一目标是实现**站点内部 (东西向流量)** 的“零信任”安全隔离。

---

## 2. 生产级完整配置示例 (最终版)

此配置引入了**按站点分离的对象组**，是现代网络配置的最佳实践。

### 2.1. 步骤一: 定义完整的、按站点分离的对象组

```shell
enable
configure

! ########## 1. CM站点对象组 ##########
object-group network CM_DCS
 host 10.10.1.10; host 10.10.1.11
object-group network CM_SERVERS
 network 10.10.1.0 0.0.0.255
object-group network CM_USERS
 network 192.168.10.0 0.0.0.255; network 192.168.40.0 0.0.0.255
object-group network CM_IOT
 network 192.168.180.0 0.0.0.255
object-group network CM_DOOR_ACCESS
 network 192.168.140.0 0.0.1.255
object-group network CM_CCTV
 network 192.168.100.0 0.0.3.255
object-group network CM_GUEST
 network 192.168.200.0 0.0.0.255
object-group network CM_DMZ
 network 192.168.240.0 0.0.0.255
object-group network CM_MGMT
 network 10.10.3.0 0.0.0.255
object-group network CM_AP_MGMT
 network 10.10.4.0 0.0.0.255
object-group network CM_IT_ADMIN
 network 10.10.15.0 0.0.0.255

! ########## 2. HZ站点对象组 ##########
object-group network HZ_DCS
 host 10.11.1.11; host 10.11.1.12
object-group network HZ_SERVERS
 network 10.11.1.0 0.0.0.255
object-group network HZ_USERS
 network 192.168.11.0 0.0.0.255; network 192.168.41.0 0.0.0.255
object-group network HZ_IOT
 network 192.168.181.0 0.0.0.255
object-group network HZ_DOOR_ACCESS
 network 192.168.142.0 0.0.1.255
object-group network HZ_CCTV
 network 192.168.104.0 0.0.3.255
object-group network HZ_GUEST
 network 192.168.201.0 0.0.0.255
object-group network HZ_DMZ
 network 192.168.241.0 0.0.0.255
object-group network HZ_MGMT
 network 10.11.3.0 0.0.0.255
object-group network HZ_AP_MGMT
 network 10.11.4.0 0.0.0.255
object-group network HZ_IT_ADMIN
 network 10.11.15.0 0.0.0.255
```

### 2.2. 步骤二: 定义ACL (最终版)

```shell
! ########## 1. 核心服务允许 (初期放通) ##########
access-list 100 remark Rule-1-Allow_Core_Services
access-list 100 permit ip any object-group CM_DCS
access-list 100 permit ip any object-group HZ_DCS

! ########## 2. IT运维区 -> ANY (最高权限) ##########
access-list 100 remark Rule-10-IT_Admin_Zone_Full_Access
access-list 100 permit ip object-group CM_IT_ADMIN any
access-list 100 permit ip object-group HZ_IT_ADMIN any

! ########## 3. 业务访问策略 (初期放通) ##########
access-list 100 remark Rule-20-Business_Access_Control
! 允许用户区访问所有服务区 (跨站+站内)
access-list 100 permit ip object-group CM_USERS object-group CM_SERVERS
access-list 100 permit ip object-group CM_USERS object-group HZ_SERVERS
access-list 100 permit ip object-group HZ_USERS object-group CM_SERVERS
access-list 100 permit ip object-group HZ_USERS object-group HZ_SERVERS
! 允许服务区之间互访
access-list 100 permit ip object-group CM_SERVERS object-group HZ_SERVERS
access-list 100 permit ip object-group HZ_SERVERS object-group CM_SERVERS
! 允许用户访问打印机
access-list 100 permit ip object-group CM_USERS object-group CM_IOT
access-list 100 permit ip object-group HZ_USERS object-group HZ_IOT

! ########## 4. 严格隔离策略 ##########
access-list 100 remark Rule-100_Strict_Zone_Isolation
! 拒绝任何流量访问管理区
access-list 100 deny ip any object-group CM_MGMT
access-list 100 deny ip any object-group HZ_MGMT
access-list 100 deny ip any object-group CM_AP_MGMT
access-list 100 deny ip any object-group HZ_AP_MGMT
! 拒绝任何流量访问隔离区
access-list 100 deny ip any object-group CM_DOOR_ACCESS
access-list 100 deny ip any object-group HZ_DOOR_ACCESS
access-list 100 deny ip any object-group CM_CCTV
access-list 100 deny ip any object-group HZ_CCTV
access-list 100 deny ip any object-group CM_IOT
access-list 100 deny ip any object-group HZ_IOT
access-list 100 deny ip any object-group CM_DMZ
access-list 100 deny ip any object-group HZ_DMZ
! 拒绝用户区之间互访
access-list 100 deny ip object-group CM_USERS object-group HZ_USERS
access-list 100 deny ip object-group HZ_USERS object-group CM_USERS
! 拒绝访客区访问任何私网地址
access-list 100 deny ip object-group CM_GUEST 10.0.0.0 0.255.255.255
access-list 100 deny ip object-group HZ_GUEST 10.0.0.0 0.255.255.255
access-list 100 deny ip object-group CM_GUEST 192.168.0.0 0.0.255.255
access-list 100 deny ip object-group HZ_GUEST 192.168.0.0 0.0.255.255

! ########## 5. 允许所有剩余流量 (确保能上网) ##########
access-list 100 remark Rule-999_Permit_Any_to_Internet
access-list 100 permit ip any any
```

### 2.3. 步骤三: 应用ACL到接口

将此ACL应用到**除IT运维(1015)和管理(1003, 1004)之外**的所有VLAN接口的**入方向**。

```shell
! ##### 应用于用户区 #####
interface vlan 10
 ip access-group 100 in
interface vlan 40
 ip access-group 100 in

! ##### 应用于物联网区 #####
interface vlan 140
 ip access-group 100 in
interface vlan 180
 ip access-group 100 in

! ##### 应用于隔离区 #####
interface vlan 101
 ip access-group 100 in
interface vlan 200
 ip access-group 100 in
interface vlan 240
 ip access-group 100 in

! ##### 应用于服务区 #####
interface vlan 1001
 ip access-group 100 in
interface vlan 1002
 ip access-group 100 in
```
