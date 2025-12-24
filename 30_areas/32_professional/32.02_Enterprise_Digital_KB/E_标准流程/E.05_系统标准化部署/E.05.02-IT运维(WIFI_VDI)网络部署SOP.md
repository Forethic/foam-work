# E.05.02-IT运维(WIFI/VDI)网络部署SOP (WPA2-Enterprise)

> **标签**: `#SOP` `#Wi-Fi` `#网络安全` `#RADIUS` `#深信服AC`
> **版本**: 3.1
> **状态**: 最终版
> **关联设计**: [[A.01-IP与VLAN分配-总表]], [[C.01-ActiveDirectory-设计规范]], [[H.05-上网行为管理部署-SOP]]

---

## 1. 目标与原则

### 1.1. 目标

为 **`VLAN 1015 (IT运维 WIFI/VDI)`** 提供一个统一、安全的接入层，无论是通过Wi-Fi物理接入，还是通过云桌面 (VDI) 虚拟接入，都能确保IT人员的设备处于专用的、隔离的运维网络中。

### 1.2. 安全原则

- **强认证**: 必须采用 `WPA2/3-Enterprise` 级别认证，将用户身份与Active Directory (AD) 域账户绑定。
- **最小权限**: 只有明确授权的AD安全组成员才能接入。
- **网络隔离**: Wi-Fi流量必须严格映射到隔离的 `VLAN 1015`。
- **日志审计**: 所有认证请求必须在深信服AC设备上留下可审计的日志。

---

## 2. 技术方案与前置准备

### 2.1. 技术方案

- **SSID**: `IT_Admin_WIFI`
- **认证**: `WPA2/3-Enterprise` with `802.1X`
- **RADIUS服务器**: **深信服上网行为管理 (SANGFOR AC)** 设备。
- **身份验证**: 深信服AC通过LDAP协议连接AD，实现账户验证。
- **VLAN映射**: 无线流量将被标记并传输在 `VLAN 1015` 中。

### 2.2. 前置准备

- 深信服AC设备已部署并正常运行，且已规划好管理IP。
- 无线控制器或AP集群的管理IP地址。
- 一个高强度的、用于RADIUS客户端和服务器之间通信的 **共享密钥**。
- AD中用于授权的 **安全组名**: `SEC_G_WIFI_IT_Admin_Access`
- 一个有权限读取AD用户信息的服务账户 (用于LDAP连接)。

---

## 3. 详细配置步骤

### 步骤1: Active Directory 配置

1. **登录域控制器**。
2. **创建授权安全组 (PowerShell)**:

    ```powershell
    # 请根据实际域名和OU结构调整 -Path 参数
    New-ADGroup -Name "SEC_G_WIFI_IT_Admin_Access" -GroupScope Global -GroupCategory Security -Path "OU=Role_Groups,OU=05_Groups,OU=_HZ_Management,DC=hzgc,DC=corp,DC=local" -Description "授权IT运维人员访问专用WIFI"
    ```

3. **将运维人员账户加入该组**:

    ```powershell
    Add-ADGroupMember -Identity "SEC_G_WIFI_IT_Admin_Access" -Members "用户名1", "用户名2"
    ```

4. **创建LDAP查询服务账户** (如果尚不存在)。

### 步骤2: 深信服AC设备 配置
>
> **核心前提**: 确保已根据 [[E.05.05-上网行为管理部署SOP]] 完成AC设备的基础部署、AD对接和RADIUS服务启用。

1. **添加RADIUS客户端**:
    - 导航至 **认证管理** -> **认证方式** -> **Radius认证**。
    - 在 **Radius客户端** 区域，点击 **新建**，将无线控制器/AP添加为客户端。
        - **IP地址**: 填写无线控制器或AP的管理IP。
        - **共享密钥**: 输入预先准备好的高强度共享密钥。
2. **创建认证策略**:
    - 导航至 **认证管理** -> **认证策略**，点击 **新建**。
    - **策略名称**: `Allow IT Admin WIFI Access`
    - **认证方式**: `Radius认证`
    - **认证服务器**: `公司AD`
    - **应用对象**: 添加 `AD/LDAP组` -> `SEC_G_WIFI_IT_Admin_Access`。
    - **动作**: `允许访问`。

### 步骤3: 无线控制器/AP 配置

1. 登录无线网络管理界面。
2. 创建一个新的WLAN/SSID:
    - **SSID名称**: `IT_Admin_WIFI`
    - **隐藏SSID (Hide SSID)**: **启用 (Enable)**
    - **安全类型**: `WPA2-Enterprise` 或 `WPA3-Enterprise`。
    - **RADIUS服务器地址**: 指向 **深信服AC设备** 的IP地址。
    - **RADIUS服务器端口**: `1812` (认证)。
    - **共享密钥**: 输入与深信服AC中完全相同的共享密钥。
    - **网络/VLAN**: 将此SSID **标记 (Tag)** 到 **`VLAN 1015`**。

### 步骤4: 交换机端口配置

- 连接AP的交换机端口必须配置为 `Trunk` 模式，并 `允许 (allow)` `VLAN 1015` 及其他所需VLAN通过。
- 具体命令请参考 [[E.04-VLAN与端口配置-SOP]]。

---

## 4. 客户端连接与验证

1. 在Windows客户端上，选择 `IT_Admin_WIFI`，点击 `连接`。
2. 输入您的 **AD域账户用户名和密码**。
3. **验证**:
    - 打开命令提示符，运行 `ipconfig`，确认获取到的IP地址在 `10.10.15.x` (CM站点) 或 `10.11.15.x` (HZ站点) 网段内。
    - 登录深信服AC设备，查看 **日志中心** -> **认证日志**，应能看到您的账户名和认证成功的记录。
