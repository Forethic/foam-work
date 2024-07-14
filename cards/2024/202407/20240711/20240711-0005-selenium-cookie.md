# 0005-selenium-cookie

## 标签

## 内容

1. browser.get_cookies(), 返回的是一个列表
   1. 列表里每个元素是一个字典，可以通过字典推导式提取其中的 name和key
2. browser.delete_all_cookies() 删除所有cookies
3. browser.delete_cookie(cookie名称) 删除指定cookie
4. browser.add_cookie( { 'name': 'cookie名称', 'key': 'cookie具体值' }) 添加cookie

## 参考
