# Visual Studio 疑难问题

## 注册码

| version |              | key                           |
| ------- | ------------ | ----------------------------- |
| vs2022  | professional | TD244-P4NB7-YQ6XK-Y8MMM-YWV2J |
| vs2022  | enterprise   | VHF9H-NXBBB-638P6-6JHCY-88JWH |
| vs2019  | professional | NYWVH-HT4XC-R2WYW-9Y3CM-X4V3Y |
| vs2019  | enterprise   | BF8Y8-GN2QH-T84XB-QVY3B-RC4DF |

## 移除许可证

1. 删除注册表
   1. vs2019 HKEY_CLASSES_ROOT\Licenses\41717607-F34E-432C-A138-A3CFD7E25CDA
   2. vs2022 HKEY_CLASSES_ROOT\Licenses\1299B4B9-DFCC-
2. 运行 visual studio installer 进行修复
