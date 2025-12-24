# C.01.1-ActiveDirectory-HZGC设计实例

> **标签**: `#AD` `#设计实例` `#HZGC`
> **关联标准**: [[C.01-ActiveDirectory-设计标准]]

---

## 1. 概述

本文档是依据《C.01-Active Directory-设计标准》为 **桦智公司 (HZGC)** 制定的具体设计实例。

## 2. 域设计详情

- **林根域名**: `corp.local`
- **子域名**: `hzgc.corp.local`
- **NetBIOS域名**: `HZGC`
- **UPN后缀 (用户登录名)**: `aaaa.com.cn`
- **林/域功能级别**: `Windows Server 2016`

## 3. 站点与域控制器 (DC)

- **站点**: A站点 (桦智)
- **域控制器**:
  - `HZ-DC01` (主)
    - **IP地址**: `10.11.1.11`
    - **所属VLAN/网段**: 1001 (服务器) / `10.11.1.0/24`
    - **角色**: DC, GC, DNS
    - **FSMO**: 持有 `hzgc.corp.local` 域的全部5个域级别角色 (PDC, RID, 基础结构)。
  - `HZ-DC02` (备)
    - **IP地址**: `10.11.1.12`
    - **所属VLAN/网段**: 1001 (服务器) / `10.11.1.0/24`
    - **角色**: DC, GC, DNS

## 4. OU (组织单位) 结构

在 `hzgc.corp.local` 域内创建以下OU结构。此结构旨在实现基于角色的管理和GPO的精确应用。

```
hzgc.corp.local
|
|-- _HZ_Management         (桦智管理根OU)
|   |
|   |-- 01_Service_Accounts  (服务账户)
|   |
|   |-- 02_Workstations      (工作站)
|   |   |-- Desktops
|   |   |-- Laptops
|   |
|   |-- 03_Servers           (服务器)
|   |   |-- Domain_Controllers
|   |   |-- App_Servers
|   |   |-- DB_Servers
|   |
|   |-- 04_Users             (用户)
|   |   |-- R&D
|   |   |-- Sales
|   |   |-- Admin
|   |   |-- Finance
|   |
|   |-- 05_Groups            (组)
|       |-- Role_Groups      (角色组 - 全局组)
|       |-- Resource_Groups  (资源组 - 域本地组)
```

## 5. 组 (Group) 设计实例 (AGDLP)

- **角色组 (全局组)**: 用于集合具有相同业务角色的用户。
  - **示例**: `HZ_GG_Sales_Users` (桦智-全局组-销售部用户)
- **资源组 (域本地组)**: 用于定义对某一具体资源的访问权限。
  - **示例**: `HZ_DL_FileShare_SalesData_Read` (桦智-域本地组-文件共享-销售数据-读取)

## 6. 组策略 (GPO) 详细配置示例

#### `HZ_SEC_Password_Policy` (密码策略)

- **链接位置**: `hzgc.corp.local` (应用到全域)
- **核心配置**:
  - **强制密码历史**: 24 个
  - **密码最长使用期限**: 42 天
  - **密码最短使用期限**: 1 天
  - **密码长度最小值**: 12 个字符
  - **密码必须符合复杂性要求**: 已启用
  - **账户锁定阈值**: 5 次无效登录
  - **账户锁定时间**: 30 分钟

#### `HZ_DEF_Desktop_Wallpaper` (桌面壁纸)

- **链接位置**: `_HZ_Management` OU
- **核心配置**:
  - **用户配置** -> **策略** -> **管理模板** -> **桌面** -> **桌面**:
    - **桌面墙纸**: 已启用, `\\hz-filesrv01\Shares\IT\Wallpaper\corp_wallpaper.jpg`
    - **不允许更改**: 已启用

---

## 7. 实施指南

本设计文档的具体部署步骤，请严格参照以下SOP执行：

- **[[C.02-ActiveDirectory-部署SOP]]**
