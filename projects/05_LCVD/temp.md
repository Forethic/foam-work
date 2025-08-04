# SmartDispenserSystem 项目

├── SmartDispenserSystem.sln                   # 解决方案文件
│
├── /Config                                    # 配置文件
│   ├── AppSettings.json                       # 路径参数、串口、光源配置等
│   └── CalibrationMatrix.json                 # 相机标定数据（像素→物理坐标）
│
├── /Core                                      # 核心功能模块
│   ├── MotionController.cs                    # XYZ运动控制（封装控制卡/串口指令）
│   ├── DispenserController.cs                 # 点胶模块控制
│   ├── LightSourceController.cs               # 光源控制（串口或 IO 控制）
│   ├── CoordinateTransformer.cs               # 坐标转换（相机像素→物理坐标）
│   └── CommandQueue.cs                        # 运动指令队列管理与执行
│
├── /Vision                                    # 图像采集与路径绘制
│   ├── CameraManager.cs                       # 相机初始化与图像流管理
│   ├── PathDrawer.cs                          # 图像上路径绘制（支持手绘或自动提取）
│   ├── PathExtractor.cs                       # 图像轮廓提取与路径解析（可结合 OpenCV）
│   └── CalibrationHelper.cs                   # 相机标定逻辑（Zhang法、单应性矩阵等）
│
├── /Models                                    # 数据模型
│   ├── Point2D.cs                             # 点位结构（像素坐标与物理坐标）
│   ├── DrawingPath.cs                         # 路径对象（点胶路径集合）
│   └── MachineCommand.cs                      # 控制指令结构体
│
├── /UI                                        # 用户界面模块
│   ├── MainWindow.xaml.cs                     # 主界面逻辑（路径绘制、控制按钮等）
│   ├── PreviewWindow.xaml.cs                  # 轨迹预览界面
│   └── UserControls/                          # 各功能模块的 UI 控件
│       ├── MotionPanel.xaml
│       ├── DispenserPanel.xaml
│       └── LightControlPanel.xaml
│
├── /Utils                                     # 工具类
│   ├── Logger.cs                              # 日志记录
│   ├── SerialHelper.cs                        # 串口封装类
│   └── ConfigManager.cs                       # 读取与保存 JSON 配置
│
├── /Resources                                 # 静态资源
│   ├── Icons/
│   └── Style.xaml                             # 公共样式（WPF）
│
└── /Docs                                       # 技术文档与使用说明
    ├── SystemArchitecture.md
    ├── SerialProtocols.md
    └── CalibrationGuide.pdf
