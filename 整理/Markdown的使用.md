---
toc:
  depth_from: 1
  depth_to: 9
  ordered: true
html:
  embed_local_images: false
  embed_svg: true
  offline: false
  toc: true

print_background: false
export_on_save:
  html: true
---

## 标题
```markdown
一级标题
=================
二级标题
-----------------

# 一级标题
## 二级标题
### 三级标题
#### 四级标题 {ignore = true}
##### 五级标题
###### 六级标题
```
## 段落

段落的换行段落的换行段落的  
换行段落的换行段落的换行（两个空格+回车）

## 字体
```markdown
*斜体文本*
_斜体文本_
**粗体文本**
__粗体文本__
***粗斜体文本***
___粗斜体文本___
```

## 分割线
```markdown
***
* * *
*****
- - -
----------
```

## 删除线
```markdown
GOOGLE.COM  
~~BAIDU.COM~~
```

## 下划线
```markdown
<u>下划线</u>
```

## 标记
==marked==

## 脚注
```markdown
创建脚注格式类似这样[^1]

[^1]: This is my first footnote  
[^2]: Visit http://ghost.org  
[^3]: A final footnote
```

## 上标下标
30^th^ `30^th^`
H~2~O `H~2~O`

## 列表
```markdown
* 第一项
* 第二项
+ 第一项
+ 第二项
- 第一项
- 第二项

1. 第一项
2. 第二项

1. 第一项：
    - 第一个元素
    - 第二个元素
2. 第二项：
    - 第一个元素
    - 第二个元素
```

## 区块
```markdown
> 区块  
> 区块  
> 区块
---
> 最外层
> > 第一层嵌套
> > > 第二层嵌套
---
> 区块中使用列表
> 1. 一
> + 一
---
* 一
    > 列表中使用区块  
    > 列表中使用区块
* 二
```

## 代码

例如 `echo()` 方法

例如 `javascript` 代码块：
```javascript{.line-numbers highlight=[2,4-5]}
$(document).ready(function () {
    alert('RUNOOB');
});
//添加 javascript{.line-numbers} 显示行号
//添加 javascript{highlight=[2,4-5]}高亮代码
```

代码高亮支持的语言：
|       名称       |         关键字          |
| :--------------: | :---------------------: |
|   AppleScript    |       applescript       |
| ActionScript 3.0 |   actionscript3, as3    |
|      Shell       |       bash, shell       |
|    ColdFusion    |     coldfusion, cf      |
|        C         |         cpp, c          |
|        C#        |   c#, c-sharp, csharp   |
|       CSS        |           css           |
|      Delphi      |   delphi, pascal, pas   |
|    diff&patch    |       diff patch        |
|      Erlang      |       erl, erlang       |
|      Groovy      |         groovy          |
|       Java       |          java           |
|      JavaFX      |       jfx, javafx       |
|    JavaScript    | js, jscript, javascript |
|       Perl       |     perl, pl, Perl      |
|       PHP        |           php           |
|       text       |       text, plain       |
|      Python      |       py, python        |
|       Ruby       |  ruby, rails, ror, rb   |
|    SASS&SCSS     |       sass, scss        |
|      Scala       |          scala          |
|       SQL        |           sql           |
|   Visual Basic   |        vb, vbnet        |
|       XML        | xml, xhtml, xslt, html  |
|   Objective C    |       objc, obj-c       |
|        F#        |   f#, f-sharp, fsharp   |
|        R         |       r, s, splus       |
|      matlab      |         matlab          |
|      swift       |          swift          |
|        GO        |       go, golang        |

## 链接
```markdown
博客链接: [Tabll](https://www.tabll.cn)
```
```markdown
链接也可以用变量来代替，文档末尾附带变量地址：  
这个链接用 1 作为网址变量 [Google][1]  
然后在文档的结尾为变量赋值（网址）

[1]: http://www.google.com/
```

## 图片
```markdown
![图片](https://laravel.com/assets/img/laravel-logo.png)

同时也支持网址变量 ![RUNOOB][2]

[2]: https://laravel.com/assets/img/laravel-logo.png

使用HTML代码设置图片的长宽等
<img src="https://laravel.com/assets/img/laravel-logo.png" width="5%">
```

## 表格
```markdown
| 表头   | 表头   |
| ------ | ------ |
| 单元格 | 单元格 |
| 单元格 | 单元格 |

对齐语法：
| 左对齐     |     右对齐 |  居中对齐  |
| :--------- | ---------: | :--------: |
| 单元格格格 | 单元格格格 | 单元格格格 |
| 单元格     |     单元格 |   单元格   |
```
```markdown
First Header | Second Header
------------ | -------------
Content from cell 1 | Content from cell 2
Content in the first column | Content in the second column
```

在 `Markdown All in one` 插件中自动对齐表格使用快捷键 `Alt` + `Shift` + `F`

自定义表格样式：
https://www.imydl.tech/ty/70.html
## HTML用法

使用 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>A</kbd> 截图

## 转义

在前面添加 `\` 以正常显示
| 符号      |   说明   |
| --------- | :------: |
| \\|反斜线 |
| \`        |  反引号  |
| \*        |   星号   |
| \_        |  下划线  |
| \{\}      |  花括号  |
| \[\]      |  方括号  |
| \(\)      |  小括号  |
| \#        |  井字号  |
| \+        |   加号   |
| \-        |   减号   |
| \.        | 英文句点 |
| \!        |  感叹号  |

## 公式
$$
\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix} 
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
\end{vmatrix}
$$

## 选项
- [ ] 未完成
- [ ] 未完成
- [ ] 已完成
- [x] 已完成
- [x] 已完成

## 缩略
*[HTML]: 这是一个提示文字
The HTML specification

```gnuplot {cmd=true output="html"}
set terminal svg
set title "Simple Plots" font ",20"
set key left box
set samples 50
set style data points

plot [-10:10] sin(x),atan(x),cos(atan(x))
```
