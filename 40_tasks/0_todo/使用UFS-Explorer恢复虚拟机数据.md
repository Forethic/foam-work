# 任务: 使用UFS Explorer恢复虚拟机数据

- **状态**: 失败/阻塞
- **优先级**: A
- **所属项目/领域**: [[30_areas/32_professional/32.01_Huazhi/_projects/Server_Recovery_Project/index]]
- **截止日期**: 2025-09-18
- **相关人员**:

---

## 目标 (Objective)

> 这个任务最终要达成的、可衡量的成果是什么？

- 使用 UFS Explorer Professional Recovery 软件，从损坏的服务器中成功提取出虚拟机的完整数据文件（如 .vmdk 或 .vhdx）。

## 关键成果 (Key Results)

> 为了达成目标，需要交付哪些具体的成果？

- [x] 制作一个包含 UFS Explorer Professional Recovery 的 Windows To Go 启动U盘。
- [x] 将启动U盘插入故障服务器，并在BIOS/UEFI中设定从U盘启动。
- [ ] 在WinPE环境中，运行UFS Explorer，对服务器内置硬盘进行扫描。（预计需要15小时，进行中）
- [ ] 定位并识别出损坏的虚拟机数据文件。
- [ ] 将虚拟机数据文件完整恢复到外接的备份硬盘中。
- [ ] 在办公电脑上（如使用 VMware Workstation 或 VirtualBox），尝试基于恢复的虚拟机磁盘文件（.vmdk/.vhdx）创建新虚拟机，并启动以验证其完整性和可引导性。

---

## 进展日志 (Progress Log)

> 在这里，通过链接每日笔记，来记录这个任务的时间线。

- **[[10_journal/2025/Week-38/2025-09-15]]**: 开始数据恢复工作。计划使用 UFS Explorer Professional Recovery 对故障服务器硬盘进行扫描和文件提取。
- **[[10_journal/2025/Week-38/2025-09-16]]**: 成功制作WTG启动盘并解决了UFS Explorer的激活问题。已启动对服务器硬盘的完全扫描，预计需要15小时，目前仍在进行中。
