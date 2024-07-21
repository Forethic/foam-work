# 0004-scrapy-dont_filter参数

## 标签

## 内容

Request 对象中存在 Dont_Filter 属性，默认值为False，表示默认过滤重复URL

spider 的内置方法 start_requests 方法中，从start_urls 中创建的请求的Dont_Filter属性值是True，表示不过滤
其他方式创建的request是过滤的，可以手动指定Dont_Filter是否过滤

## 参考
