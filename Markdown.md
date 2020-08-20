# 标题一
## 标题二
### 标题三
#### 标题四
##### 标题五
###### 标题六

[http://www.google.com](#user-content-锚点)

> 这里是第一级引用  
>  
> > 这里是第二级引用  
> 
> 现在回到第一级引用

> ## 这是一个标题
> 1. 这是第一个列表项
> 2. 这是第二个列表项
>
> 给出一些例子
> return shell_exec('`echo $input | $markdown_script`'); 

- 无序列表1
- 无序列表2
- 无序列表3

+ 无序列表1
+ 无序列表2
+ 无序列表3

* 无序列表1
* 无序列表2
* 无序列表3

1. 有序列表1
2. 有序列表2
3. 有序列表3

* WEB开发涉及到的一些知识
    > HTML  
    > CSS  
    > JavaScript  

- [ ] 不勾选
- [x] 勾选

换行(末尾添加两个空格)  
换行(使用空行来换行)

换行(使用空行来换行)

``` bash
ps -ef | grep "composer"
```

```ruby
require 'redcarpet'
markdown = Redcarpet.new("Hello World!")
puts markdown.to_html
```

_斜体，没有空格_  
*斜体，没有空格*  

__粗体，没有空格__  
**粗体，没有空格**  

___斜体加粗___  
***斜体加粗***  

[这是一个超链接](https://google.com)

<https://google.com>

[1]: https://google.com
[2]: https://developer.mozilla.org
[高级链接，用变量来代替][1]  
[MDN web docs][2]  
  
First Header | Second Header | Third Header
-------------|---------------|-------------
Content      |Content        |Content
Content      |Content        |Content

First Header | Second Header | Third Header
:------------|:-------------:|------------:
Left         |Center         |Right
Left         |Center         |Right


这是分割线的上部分内容  
***
这是分割线的下部分内容  

~~删除线，没有空格~~

<u>这里是下划线</u>

[^脚注，是对文本的补充说明]

![Alt text](https://www.mozilla.org/media/protocol/img/logos/firefox/browser/logo-word-hor-white-sm.3b4af7c9dc52.png "This is image Title")

[3]: https://www.mozilla.org/media/protocol/img/logos/firefox/browser/logo-word-hor-white-sm.3b4af7c9dc52.png "像对待链接那样使用图片"
![像对待链接那样使用图片][3]

目前支持的 HTML 元素有：`<kbd> <b> <i> <em> <sup> <br>` 等  
如：<kbd>Ctrl</kbd> + <kbd> Alt </kbd> + <kbd> Del </kbd>

转义：  
\\      反斜线  
\`      反引号  
\*      星号  
\_      下划线  
\{\}    花括号  
\[\]    方括号  
\(\)    小括号  
\#      井字号  
\+      加号  
\-      减号  
\.      英文句点  
\!      感叹号  

$$
    \mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix} 
    \mathbf{i} & \mathbf{j} & \mathbf{k} \\
    \frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
    \frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
    \end{vmatrix}
    $$tep1}{\style{visibility:hidden}{(x+1)(x+1)}}
$$  

```graph
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->E;
    E-->F;
    D-->F;
    F-->G;
```

```graph
sequenceDiagram
    participant Alice
    participant Bob
    Alice->John: Hello John, how are you?
    loop Healthcheck
        John->John: Fight against hypochondria
    end
    Note right of John: Rational thoughts 
prevail...
    John-->Alice: Great!
    John->Bob: How about you?
    Bob-->John: Jolly good!
```

```graph
gantt
        dateFormat  YYYY-MM-DD
        title Adding GANTT diagram functionality to mermaid
        section A section
        Completed task            :done,    des1, 2014-01-06,2014-01-08
        Active task               :active,  des2, 2014-01-09, 3d
        Future task               :         des3, after des2, 5d
        Future task2               :         des4, after des3, 5d
        section Critical tasks
        Completed task in the critical line :crit, done, 2014-01-06,24h
        Implement parser and jison          :crit, done, after des1, 2d
        Create tests for parser             :crit, active, 3d
        Future task in critical line        :crit, 5d
        Create tests for renderer           :2d
        Add to mermaid                      :1d
```