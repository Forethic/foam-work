# Git 疑难问题

## Config

## Git LFS

> git clone xxxx.git 时碰到以下错误:
> git-lfs filter-process: line 1: git-lfs: command not found

Git LFS 是一个 Git 扩展，用于管理和存储大文件（如图像、视频或其他大文件类型）。
当项目使用 Git LFS 时，Git 不会直接存储这些文件，而是存储指向它们的指针。
需要 Git LFS 来下载实际文件。

### 解决步骤

1. 安装 Git LFS

- Windows
  - 下载 [Git LFS 官方下载页面](https://git-lfs.github.com/)
  - 双击安装
- MacOS:
  - brew install git-lfs
- Linux
  - sudo apt-get install git-lfs

2. 初始化 Git LFS

- 打开一个新的终端，输入以下命令

```bash {.line-numbers}
git lfs install
```

3. 重试克隆
