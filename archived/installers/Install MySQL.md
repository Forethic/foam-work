# MySQL 安装教程

## 内容准备

[MySQL 压缩包](../../archived/installers/mysql-8.0.34-winx64.zip)

## 安装步骤

1. 将压缩包解压到指定的路径下
2. 以管理员身份运行终端，并定位到安装路径

```bash {.line-numbers}
# 配置mysql安装模式
.\mysqld.exe --initialize-insecure --user=mysql

# 执行安装
.\mysqld.exe -install

# 启动服务
net start MySQL

# 修改root用户密码
.\mysql.exe -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';

# 使修改生效
FLUSH PRIVILEGES;
```

3. 将路径添加到系统的环境变量 `安装路径\bin`