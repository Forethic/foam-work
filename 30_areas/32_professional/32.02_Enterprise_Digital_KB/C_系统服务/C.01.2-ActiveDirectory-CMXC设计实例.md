# C.01.2-ActiveDirectory-CMXC设计实例

> **标签**: `#AD` `#设计实例` `#CMXC`
> **关联标准**: [[C.01-ActiveDirectory-设计标准]]

---

## 1. 概述

本文档是依据《C.01-Active Directory-设计标准》为 **纯铭公司 (CMXC)** 制定的具体设计实例。

## 2. 域设计详情

- **林根域名**: `corp.local`
- **子域名**: `cmxc.corp.local`
- **NetBIOS域名**: `CMXC`
- **UPN后缀 (用户登录名)**: `bbbb.com`
- **林/域功能级别**: `Windows Server 2016`

## 3. 站点与域控制器 (DC)

- **站点**: B站点 (纯铭)
- **域控制器**:
  - `ROOT-DC01` (林根DC)
    - **IP地址**: `10.10.1.10`
    - **所属VLAN/网段**: 1001 (服务器) / `10.10.1.0/24`
    - **角色**: DC, GC, DNS
    - **FSMO**: 持有林级别的全部2个角色 (架构主控, 域命名主控)。
  - `CM-DC01` (主)
    - **IP地址**: `10.10.1.11`
    - **所属VLAN/网段**: 1001 (服务器) / `10.10.1.0/24`
    - **角色**: DC, GC, DNS
    - **FSMO**: 持有 `cmxc.corp.local` 域的全部5个域级别角色 (PDC, RID, 基础结构)。

## 4. OU (组织单位) 结构

在 `cmxc.corp.local` 域内创建以下OU结构。此结构旨在实现基于角色的管理和GPO的精确应用。

```
cmxc.corp.local
|
|-- _CM_Management         (纯铭管理根OU)
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
|   |   |-- Production
|   |   |-- Admin
|   |   |-- Warehouse
|   |
|   |-- 05_Groups            (组)
|       |-- Role_Groups      (角色组 - 全局组)
|       |-- Resource_Groups  (资源组 - 域本地组)
```

## 5. 组 (Group) 设计实例 (AGDLP)

- **角色组 (全局组)**: 用于集合具有相同业务角色的用户。
  - **示例**: `CM_GG_Production_Users` (纯铭-全局组-生产部用户)
- **资源组 (域本地组)**: 用于定义对某一具体资源的访问权限。
  - **示例**: `CM_DL_ERP_Module_Production_Write` (纯铭-域本地组-ERP生产模块-写入)

## 6. 组策略 (GPO) 详细配置示例

#### `CM_SEC_Screen_Lock_Policy` (屏幕锁定策略)

- **链接位置**: `_CM_Management` OU
- **核心配置**:
  - **用户配置** -> **策略** -> **管理模板** -> **控制面板** -> **个性化**:
    - **屏幕保护程序**: 已启用
    - **屏幕保护程序可执行文件名**: `scrnsave.scr`
    - **密码保护屏幕保护程序**: 已启用
    - **屏幕保护程序超时**: 900 秒 (15分钟)

---

## 7. 实施指南

本设计文档的具体部署步骤，请严格参照以下SOP执行：

- **[[C.02-ActiveDirectory-部署SOP]]**
