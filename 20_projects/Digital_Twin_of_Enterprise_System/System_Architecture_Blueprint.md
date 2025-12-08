# 系统架构蓝图 (System Architecture Blueprint)

**标签**: #核心蓝图

这是指导企业信息化建设的顶层设计图，描绘了系统的整体结构和模块关系。

```mermaid
graph TD
    subgraph "门户层 (Portal Layer)"
        U_EMP[员工门户]
        U_MGR[管理驾驶舱]
        U_CUS[客户门户]
        U_SUP[供应商门户]
    end
    subgraph "集成层 (Integration Layer)"
        I_FIN[财务软件接口]
        I_IM[钉钉/企微接口]
        I_API[开放API平台]
        I_IOT[IoT/硬件接口]
    end
    subgraph "应用层 (Application Layer)"
        A1[[Biz_L2C|L2C]] & A2[[Biz_P2P|P2P]] & A3[[Biz_P2I|P2I]]
        B1[[Mgmt_OA|OA]] & B2[[Mgmt_HRM|HRM]] & B3[[Mgmt_PM|PM]]
        C1[[DA_BI_Dashboard|BI仪表盘]]
    end
    subgraph "数据层 (Data Layer)"
        D1[[MD_Material|物料]] & D2[[MD_Customer|客户]] & D3[[MD_Vendor|供应商]] & D4[[MD_Employee|员工]]
        D_BIZ[业务数据]
    end
    subgraph "基础设施层 (Infrastructure Layer)"
        P_LCDP((低代码 PaaS 平台))
    end
    P_LCDP --> D1 & D2 & D3 & D4 & D_BIZ
    D1 & D2 & D3 & D4 & D_BIZ --> A1 & A2 & A3 & B1 & B2 & B3
    D_BIZ --> C1
    A1 & A2 & A3 & B1 & B2 & B3 --> U_EMP
    C1 --> U_MGR
    A1 --> U_CUS
    A2 --> U_SUP
    A2 --> I_FIN
    B1 & B2 --> I_IM
    A3 --> I_IOT
    P_LCDP --> I_API
