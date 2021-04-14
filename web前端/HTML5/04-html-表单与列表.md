#### 一、表单

- 服务器收集用户数据的方式

```html
<!DOCTYPE html>
<html lang='zh-CN'>
    <head>
        <meta charset='utf-8'>
        <meta name='keyword' content='html,html5'>
        <meta name='description' content='一个神奇的网站'>
        <title>html5</title>
    </head>
    <body>
        <img src='./1.png' alt='上传的风景图'/>
    </body>
</html>
```

#### 二、链接

- 网站的所有内容无法在一个页面上展示，需要分成多个页面
- 跳转到其他的页面。

```html
<!DOCTYPE html>
<html lang='zh-CN'>
    <head>
        <meta charset='utf-8'>
        <meta name='keyword' content='html,html5'>
        <meta name='description' content='一个神奇的网站'>
        <title>html5</title>
    </head>
    <body>
        <!--常用的链接跳转-->
        <a href='http://www.baidu.com' target='_self'>当前页面打开百度</a>
        <a href='http://www.baidu.com' target='_blank'>新窗口打开百度</a>
        
        <!--锚点使用-->
        <a href='#comment-1'>当前页面的跳转，俗称锚点</a>
        <div id='comment-1'>
            
        </div>
        
        <!--发邮件、打电话-->
        <a href='mailto:920288326@qq.com'>发邮件给我们</a>
        <a href='tel:18338467736'>联系客服</a>
        
        <!--下载文件-->
        <a href='/upload/1023/11/25/0001.zip' download=''>点我下载</a>
    </body>
</html>
```































