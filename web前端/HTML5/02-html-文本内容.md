#### 一、基本标签

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
        <p>
            块级-表示一个段落，换行和空白会被删除
        </p>
        <pre>
        	块级-表示一个段落，换行和空白会被保留，字体缩小一级
        </pre>
        <br/> 换一行
        <span>表示一些文字，如果这些文字没有合适的语意</span>
    </body>
</html>
```

#### 二、描述文本标签

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
        嘎嘎
        <small>行级-用来描述、声明时使用，字体缩小一级</small>

        <time>行级-用来显示时间时使用</time>

        <abbr title="用来描述缩写的内容">描述缩写的内容</abbr>

        <!--上标-->
        3<sup>2</sup> + 4<sup>2</sup> = 5<sup>2</sup>
        <!--下标-->
        H<sub>2</sub>SO<sub>4</sub>

        <!--删除+强调时同时使用-->
        请叫我<del>Liu Jie</del><ins>Liu YaJie</ins>
        <s>请把我删除吧</s>

        <!--代码，需要结合代码插件使用-->
        <code>echo $username;</code>
        <!--进度条，内容是进度条不能使用时展示用的-->
        <progress value="99" max="100" title="99">100%</progress>

        <!--强调-->
        <strong>粗体强调</strong>
        <em>斜体强调</em>

        <!--引用-->
        <blockquote cite="http://www.baidu.com">
        块级-引用另一个网站的话或者内容
        </blockquote>
        <q>
        行级-会加上引号的
        </q>---<cite>刘亚杰</cite>

        <!--地址-->
        <address>
            liuyajie <br/>
            tel:18338467736
            email:920288326@qq.com
        </address>
    </body>
</html>
```































