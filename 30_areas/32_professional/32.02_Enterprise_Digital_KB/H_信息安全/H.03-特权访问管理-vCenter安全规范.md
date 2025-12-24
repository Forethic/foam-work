# H.03-特权访问管理-vCenter安全规范

> **标签**: `#安全规范` `#特权访问` `#vCenter` `#身份认证` `#纵深防御`
> **版本**: 2.0 (最终版)
> **状态**: 已发布
> **关联标准**: [[A.01-IP与VLAN分配-总表]], [[E.05.06-VLAN间访问控制(ACL)配置SOP]]

---

## 1. 目的与原则

### 1.1. 目的

本规范旨在通过设计一套**纵深防御**体系，对vCenter及ESXi主机的访问进行严格管控，确保只有授权的人员，在受信任的环境下，通过强化的身份认证，才能执行特权操作。

### 1.2. 核心原则

- **最小权限**: 任何用户或系统只应被授予完成其任务所必需的最小权限。
- **网络隔离**: 必须从网络层面限制对vCenter/ESXi管理界面的访问。
- **身份绑定**: 所有操作必须能追溯到唯一的个人身份，严禁共享内置高权限账户。
- **纵深防御**: 任何单一安全层的失效，都不应导致整个系统的失陷。

---

## 2. 访问控制分层设计 (实施路线图)

采用“网络准入 -> 身份认证 -> 多因素认证”三层模型，并遵循“小步快跑、分阶段实施”的原则。

### 阶段一：网络层隔离 (基础实现 - 当前状态)

此为必须立即实施的基础安全措施，确保只有在`IT运维VLAN`的设备才能“看到”vCenter/ESXi的管理页面。

1. **管理平面网络规划**:
    - **vCenter/ESXi管理接口**: 统一规划在 **`VLAN 1003 (网络设备管理)`**。
    - **硬件带外管理 (iLO/iDRAC)**: 统一规划在 **`VLAN 1002 (服务器硬件管理)`**。
    - **IT核心运维VLAN**: **`VLAN 1015`**，专用于IT工程师执行特权管理任务。

2. **访问控制实现**:
    - 通过在**核心交换机**上部署的ACL策略实现。
    - **核心规则**: `access-list 100 deny ip any object-group MGMT_ZONE`。
    - **效果**: 只有来自`IT_ADMIN_ZONE (VLAN 1015)`的流量被允许访问`MGMT_ZONE (VLAN 1003)`，其他所有VLAN的访问均被拒绝。
    - **详细配置**: 参考 **[[E.05.06-VLAN间访问控制(ACL)配置SOP]]**。

### 阶段二：身份层整合 (进阶实现 - 短期规划)

此阶段的目标是将vCenter的权限管理与企业统一的身份系统（Active Directory）集成。

1. **vCenter加入AD域**。
2. **创建AD安全组**:
    - `G-vCenter-Admins`: vCenter超级管理员
    - `G-vCenter-VM-Operators`: 虚拟机操作员
3. **vCenter角色映射**:
    - 将AD安全组映射到vCenter的内置角色 (Administrator, Virtual Machine Power User 등)。
4. **日常管理**:
    - **禁用内置账户**: 日常运维中应**严禁**使用`administrator@vsphere.local`账户。
    - 通过管理AD组成员来实现vCenter权限的授予与回收。

### 阶段三：多因素认证 (MFA) (最佳实践 - 长期规划)

此为最高安全级别的保障措施，防止因密码泄露导致的安全事件。

1. **技术路径**: vCenter通过SAML/OIDC协议与支持MFA的身份提供商 (IdP) 进行联合身份验证。
2. **推荐IdP**: Microsoft Entra ID (原Azure AD), Okta等。
3. **实施概要**:
    - 在vCenter中配置一个新的身份源，指向IdP。
    - 在IdP中为vCenter配置SSO，并强制要求MFA。
4. **最终效果**: 用户访问vCenter时，将被重定向到IdP，在完成MFA后才能登录。

---

## 3. 架构与流量示意图

```mermaid
graph TD
    subgraph "IT运维区 (VLAN 1015)"
        AdminPC("fa:fa-laptop IT管理员")
    end
    subgraph "管理平面 (VLAN 1003)"
        vCenter("vCenter Server")
        ESXi_Mgmt("ESXi Host Mgmt")
    end
    subgraph "业务平面 (VLAN 1001)"
        VM1("VM-1 (Web Server)")
    end
     subgraph "用户区 (VLAN 10)"
        User_PC("普通用户PC")
    end

    AdminPC -- "✅ 管理流量 (HTTPS/SSH)" --> vCenter & ESXi_Mgmt
    User_PC -- "✅ 业务流量 (HTTP)" --> VM1
    User_PC -- "❌ ACL阻断" --> vCenter & ESXi_Mgmt
