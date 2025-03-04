# 数据接口

## 体感服数据

### 电刺激

| Property Name     | Value Range | Default Value | Limit                      | Remark   |
| ----------------- | ----------- | ------------- | -------------------------- | -------- |
| Current Intensity | 1-100       | 1             |                            | 电流强度 |
| Input Index       | 1,2,3,4     | 1             | 与 Output Index 值不能相同 | 输入点位 |
| Output Index      | 1,2,3,4     | 2             | 与 Input Index 值不能相同  | 输出点位 |
| Pulse             | 1-15        | 1             |                            | 乘以10   |
| Start Time        | 10-∞        | 10            | 必须从10开始               |          |

## 后端接口
