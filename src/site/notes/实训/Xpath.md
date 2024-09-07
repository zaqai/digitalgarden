---
{"dg-publish":true,"permalink":"/实训/Xpath/"}
---


# 爬虫数据提取——Xpath

## 爬虫数据提取——方法总结

我们获取了想要的html页面之后，接下来的问题就是如何将我们需要的数据给提取下来，一般来说有三种方式，分别是Xpath语法，正则表达式和bs4库。
![image.png|578](https://oss.zaqai.com/img/202409061045306.png)

- 解析越快的，用起来肯定越难
- 解析越慢的，用起来肯定更简单一些
- 所谓“鱼与熊掌不可兼得”

## 爬虫数据提取——Xpath环境配置

Xpath（XML Path Language）是一门在XML和HTML文档中查找信息的语言，可用来在XML和HTML文档中对元素和属性进行遍历。

- 简单来说，我们的数据是超文本数据，想要获取超文本数据里面的内容，就要按照一定规则来进行数据的获取，这种规则就叫做Xpath语法。

## Xpath语法选取数据逻辑

- HTML页面是由标签构成的，这些标签就像整个族谱一样排列有序
- 所有的HTML标签都有很强的层级关系，正是基于这种层级关系，Xpath语法能够选择出我们想要的数据。

### 示例家族层级关系

```
xxx >> 太爷爷 >> 爷爷
爷爷 >> 爸爸 >> 儿子 >>
孙子 >> xxx
```

## Xpath语法

以百度为例，我们来学习Xpath语法。
![image.png](https://oss.zaqai.com/img/202409061049282.png)

1. **节点选取**

   - `//` 表示全局搜索，`//div`即全局搜索所有的div标签
   - `/` 表示标签下的局部搜索，`//head/meta`即表示head标签下所有的meta标签
例如：
- `//head/meta[@http-equiv]`，提取head下的meta中含有http-equiv属性的部分。

2. **谓语**
![image.png](https://oss.zaqai.com/img/202409061051118.png)

   - `[k]` 表示当前位置下的第k个标签
   - `[last()]` 表示最后一个标签。

3. **通配符**
![image.png](https://oss.zaqai.com/img/202409061052290.png)

   - `//title[@*]`提取所有含有属性的title标签。

4. **选取多个路径**

   - 使用 `|` 来表示选择多个路径：
     - `eg: //meta | // title` ->> 选择所有的meta和title.

## Xpath语法应用实战
https://movie.douban.com/subject/1291546/
- 抓取电影名
- 抓取导演和主演等相关信息 
- 获取热门评论的评论者和评论内容


