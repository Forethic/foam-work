# MongoDB 安装教程

## 内容装备

[MongoDB 安装包](../../archived/installers/mongodb-win32-x86_64-2012plus-4.2.24-signed.msi)

## 安装步骤

1. 双击安装包
2. 一路默认
3. 设定 不安装 compass
4. 设定环境变量 `C:\Program Files\MongoDB\Server\4.2\bin`
5. 设定配置

> Warning:
> 存储路径设定成其他盘或者不是C盘的软键盘，不然会存在文件权限问题

```bash {.line-numbers}
# 设定开机自启和db存储路径 log存储路径
.\mongod.exe --logpath "C:\Users\Jonat\Documents\MongoDB\logs" --logappend --dbpath "C:\Users\Jonat\Documents\MongoDB\db" --directoryperdb --serviceName MongoDB --install
```
