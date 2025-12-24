# C.03-ADFS-设计规范 (v1.0)

> **标签**: `#ADFS` `#SSO` `#身份联邦` `#设计规范`
> **版本**: 1.0
> **状态**: 草稿

---

## 1. 概述与目标

本文档为集团统一身份认证平台 **Active Directory Federation Services (ADFS)** 的设计与部署提供标准规范。

目标是构建一套高可用的、安全的集中式身份认证服务，为内部应用和第三方云服务（如Office 365、企业邮箱等）提供基于AD账户的**单点登录 (Single Sign-On, SSO)** 和身份验证。

## 2. 部署架构

为实现安全与高可用的目标，我们采用业界标准的**ADFS服务器场 + WAP代理**的部署模型。

- **ADFS服务器场 (内部网络)**:
  - **位置**: 部署在核心的服务器网段 (VLAN 1001)。
  - **组件**: 至少两台ADFS服务器组成一个服务器场 (Farm)，通过网络负载均衡器 (NLB) 对外提供一个统一的虚拟IP (VIP)。
  - **通信**: 直接与各自域的域控制器 (DC) 通信以验证用户凭据。
- **Web应用程序代理 (WAP) 服务器 (DMZ)**:
  - **位置**: 部署在DMZ隔离区 (VLAN 999)。
  - **组件**: 至少两台WAP服务器，同样通过NLB对外发布。
  - **通信**: 作为ADFS服务面向互联网的**安全反向代理**。它们接收所有来自外部的认证请求，并将其安全地转发给内部的ADFS服务器场。WAP服务器本身不加入域，也不直接与DC通信。

### 架构图

```mermaid
graph TD
    subgraph "互联网"
        User[用户]
    end

    subgraph "DMZ (VLAN 999)"
        WAP_NLB[WAP 负载均衡器]
        WAP1[WAP Server 1]
        WAP2[WAP Server 2]
    end

    subgraph "内部网络 (VLAN 1001)"
        ADFS_NLB[ADFS 负载均衡器]
        ADFS1[ADFS Server 1]
        ADFS2[ADFS Server 2]
        DC_HZ[HZ-DC01 @ .11]
        DC_CM[CM-DC01 @ .21]
    end

    User -->|sso.company.com| WAP_NLB
    WAP_NLB --> WAP1
    WAP_NLB --> WAP2
    
    WAP1 -->|HTTPS (443)| ADFS_NLB
    WAP2 -->|HTTPS (443)| ADFS_NLB

    ADFS_NLB --> ADFS1
    ADFS_NLB --> ADFS2

    ADFS1 --> DC_HZ
    ADFS1 --> DC_CM
    ADFS2 --> DC_HZ
    ADFS2 --> DC_CM
```

## 3. 认证流程 (外部用户示例)

1. 用户访问云服务 (e.g., `mail.bbbb.com`)。
2. 云服务将用户重定向到公司的统一登录地址 `sso.company.com` (此域名指向WAP的公网IP)。
3. 用户在WAP呈现的登录页面输入UPN (`user@bbbb.com`) 和密码。
4. WAP将认证请求安全转发给内部的ADFS服务器场。
5. ADFS服务器根据UPN后缀，识别出用户属于`chunming.corp.local`域，并将请求交由该域的DC进行验证。
6. DC验证成功后，ADFS服务器生成一个加密的安全令牌 (Token)。
7. 该令牌通过用户浏览器传回给云服务。
8. 云服务验证令牌有效，允许用户登录。

## 4. 设计要点

- **UPN与公共域名一致**: 这是实现无缝SSO和良好用户体验的**关键前提**。用户使用其电子邮件地址作为所有服务的登录名。
- **高可用性**: 所有组件 (ADFS, WAP) 都必须以集群和负载均衡的方式部署，避免单点故障。
- **带宽需求**: ADFS认证流量本身极小 (KB级别)，对互联网出口带宽没有特殊要求。稳定性远比带宽重要。
- **服务器资源**: ADFS和WAP服务器对CPU和内存有一定要求（用于加解密），建议从 `2 vCPU / 4GB RAM` 起步。
