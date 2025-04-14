# CVD程序改造

## 250407 CVD程序尝试运行

用时: 09:00 - 12:00

1. mysql 配置成无密码登录
2. LCVDType 连接配置成root登录方式，避免报错
3. mysql 中没有对应的数据库名称，需要确认

## 250408 CVD程序内容为空排查

用时: 14:00 - 16:30

1. 执行程序前先要将COM Dll注册，执行脚本
2. 弹窗错误
   1. No database selected
      1. SELECT RECIPE_NAME FROM DEP_ZAP_TBL GROUP BY RECIPE_NAME;
   2. 请重新启动 CVD 程序！！
      1. 检测到 CVD System 程序未启动
   3. 构建CVD System源码构建环境
      1. 源码压缩包加密
3. 与许总沟通尝试获取无加密
4. 尝试暴力破解
5. 许总提供密钥解决了

## 250414 CVDSystem 源码构建+数据库导入

- [ ] CVD System 源码构建
- [ ] 数据库数据导入
