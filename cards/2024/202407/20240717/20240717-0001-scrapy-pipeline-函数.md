# 0001-scrapy-pipeline-函数

## 标签

## 内容

### process_item(self, item, spider)

- 管道类中必须要有的方法
- 实现对item数据的处理
- 一般都会return item，会传递给权重低的process_item, 如果管道只有一个，可以不用写
- return None的话，会抛弃数据

> 判断item是否符合要求
> 1.不满足要求
> -- 调用scrapy内置异常将item丢弃：raise DropItem
> -- 使用return None 丢弃数据

### open_spider(self, spider)

- 爬虫开启时执行一次
- 可以在该方法中建立数据库连接，打开文件对象

### close_spider(self, spider)

- 爬虫关闭时执行一次
- 可以在该方法中关闭数据库连接，关闭文件对象

## 参考
