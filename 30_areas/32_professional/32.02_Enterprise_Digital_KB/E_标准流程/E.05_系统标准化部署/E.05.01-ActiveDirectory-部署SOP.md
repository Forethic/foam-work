# E.05-Active Directory-部署SOP (多域森林版)

> **标签**: `#SOP` `#AD` `#多域森林` `#部署`
> **版本**: 3.0
> **状态**: 最终版
> **关联设计**: [[C.01-ActiveDirectory-设计规范]], [[C.01.1-ActiveDirectory-HZGC设计实例]], [[C.01.2-ActiveDirectory-CMXC设计实例]]

---

## 1. 概述

本SOP (Standard Operating Procedure) 提供了在公司环境中部署**多域AD森林**的标准化、详细的作业步骤。此流程涉及多个站点和域的协同工作，为保证部署的成功和一致性，所有操作必须严格按照本文档顺序执行。

**核心部署顺序**:

1. **阶段一**: 在纯铭(CM)站点部署 **林根域** (`corp.local`)。
2. **阶段二**: 在纯铭(CM)站点部署 **`cmxc` 子域**。
3. **阶段三**: 在桦智(HZ)站点部署 **`hzgc` 子域**。
4. **阶段四**: 完成所有部署后的验证和基础配置。

---

## 2. 前置条件与准备

在开始部署前，请确保以下所有条件均已满足。

### 2.1. 服务器资源规划

| 主机名      | 操作系统                 | vCPU | 内存 (GB) | 磁盘 (GB) | 站点 | 用途           |
| :---------- | :----------------------- | :--- | :-------- | :-------- | :--- | :------------- |
| `ROOT-DC01` | Windows Server 2019/2022 | 2    | 4         | 100       | CM   | 林根域控制器   |
| `CM-DC01`   | Windows Server 2019/2022 | 2    | 4         | 100       | CM   | `cmxc`子域DC |
| `HZ-DC01`   | Windows Server 2019/2022 | 2    | 4         | 100       | HZ   | `hzgc`子域DC   |

### 2.2. IP地址规划

| 主机名      | IP 地址       | 子网掩码        | 网关            | 主DNS         | 备DNS    |
| :---------- | :------------ | :-------------- | :-------------- | :------------ | :------- |
| `ROOT-DC01` | `10.10.1.10`  | `255.255.255.0` | `10.10.1.254`   | `127.0.0.1`   | -        |
| `CM-DC01`   | `10.10.1.11`  | `255.255.255.0` | `10.10.1.254`   | `10.10.1.10`  | `127.0.0.1` |
| `HZ-DC01`   | `10.11.1.11`  | `255.255.255.0` | `10.11.1.254`   | `127.0.0.1`   | `10.10.1.10`  |

> **重要**:
>
> - **DNS配置原则**: 为保证性能和高可用，域控制器(DC)的 **主DNS应始终指向自己 (127.0.0.1)**，备用DNS指向林内另一台DC。
> - **部署前置条件**: 在执行域提升操作之前，服务器的主DNS必须 **临时** 指向一台能解析林信息的现有DC (如 `ROOT-DC01` 的 `10.10.1.10`)。提升成功并重启后，再按照上表将其改回最终配置。

### 2.3. 网络连通性

- 必须确保桦智(HZ)与纯铭(CM)站点之间的IPsec VPN隧道已建立且工作正常。
- 从 `HZ-DC01` 必须能够 `ping` 通 `ROOT-DC01` 和 `CM-DC01` 的IP地址。

---

## 3. 部署步骤 (PowerShell)

为提高效率和标准化，推荐使用PowerShell执行以下部署。

### 3.1. 步骤一: 部署林根域 (`ROOT-DC01`)

1. **安装AD服务角色**:

    ```powershell
    Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
    ```

2. **提升为新林的第一个DC**:

    ```powershell
    Install-ADDSForest `
        -DomainName "corp.local" `
        -DomainNetbiosName "CORP" `
        -DomainMode Win2016 `
        -ForestMode Win2016 `
        -InstallDns `
        -NoRebootOnCompletion
    ```

3. **重启服务器**: `Restart-Computer -Force`

### 3.2. 步骤二: 部署第一个子域 (`CM-DC01`)

1. **安装AD服务角色**:

    ```powershell
    Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
    ```

2. **提升为现有林中的新子域**:

    ```powershell
    # 注意: 需要输入林根域的管理员凭据
    Install-ADDSDomain `
        -NewDomainName "cmxc.corp.local" `
        -ParentDomainName "corp.local" `
        -NewDomainNetbiosName "CMXC" `
        -DomainMode Win2016 `
        -InstallDns `
        -Credential (Get-Credential) `
        -NoRebootOnCompletion
    ```

3. **重启服务器**: `Restart-Computer -Force`

### 3.3. 步骤三: 部署第二个子域 (`HZ-DC01`)

1. **安装AD服务角色**:

    ```powershell
    Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
    ```

2. **提升为现有林中的新子域**:

    ```powershell
    # 注意: 需要输入林根域的管理员凭据
    Install-ADDSDomain `
        -NewDomainName "hzgc.corp.local" `
        -ParentDomainName "corp.local" `
        -NewDomainNetbiosName "HZGC" `
        -DomainMode Win2016 `
        -InstallDns `
        -Credential (Get-Credential) `
        -NoRebootOnCompletion
    ```

3. **重启服务器**: `Restart-Computer -Force`

---

## 4. 部署后任务

在所有DC都成功部署并重启后，执行以下关键配置。

1. **创建OU结构**:
    - 登录到每个子域的DC (`CM-DC01`, `HZ-DC01`)。
    - 打开“Active Directory 用户和计算机”。
    - 根据 [[C.01.1-ActiveDirectory-HZGC设计实例]] 和 [[C.01.2-ActiveDirectory-CMXC设计实例]] 文档中定义的详细OU结构，完成OU的创建。

2. **配置GPO**:
    - 在每个子域的DC上，打开“组策略管理”。
    - 根据设计实例中定义的GPO参数，创建并链接相应的组策略。

3. **注册备用UPN后缀**:
    - 登录到林根DC (`ROOT-DC01`)。
    - 打开“Active Directory 域和信任关系”。
    - 右键点击根节点，选择“属性”，在“UPN后缀”标签页中，添加 `aaaa.com.cn` 和 `bbbb.com`。

---

## 5. 最终验证

- **FSMO角色验证**:
  - 在`ROOT-DC01`上运行: `netdom query fsmo` (应显示2个林角色)。
  - 在`CM-DC01`上运行: `netdom query fsmo` (应显示5个域角色)。
  - 在`HZ-DC01`上运行: `netdom query fsmo` (应显示5个域角色)。
- **诊断测试**:
  - 在每台DC上运行 `dcdiag /v /c /e /q`，检查是否有错误。
- **复制状态验证**:
  - 在每台DC上运行 `repadmin /showrepl`，确保所有入站和出站复制都成功。
  - 运行 `repadmin /syncall /AdeP` 强制同步所有分区，检查是否报错。
