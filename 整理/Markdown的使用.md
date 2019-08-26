# 标题

一级标题
=================
二级标题
-----------------

# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题

# 段落

段落的换行段落的换行段落的  
换行段落的换行段落的换行（两个空格+回车）

# 字体

*斜体文本*  
_斜体文本_  
**粗体文本**  
__粗体文本__  
***粗斜体文本***  
___粗斜体文本___  

# 分割线

***
* * *
*****
- - -
----------

# 删除线

GOOGLE.COM  
~~BAIDU.COM~~

# 下划线

<u>下划线</u>

# 脚注

创建脚注格式类似这样[^1]

[^1]: This is my first footnote  
[^2]: Visit http://ghost.org  
[^3]: A final footnote

# 列表

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

# 区块

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

# 代码

例如 `echo()` 方法

例如 `javascript` 代码块：
```javascript
$(document).ready(function () {
    alert('RUNOOB');
});
```

# 链接

博客链接: [Tabll](https://www.tabll.cn)


链接也可以用变量来代替，文档末尾附带变量地址：  
这个链接用 1 作为网址变量 [Google][1]  
然后在文档的结尾为变量赋值（网址）

[1]: http://www.google.com/

# 图片

![图片](https://laravel.com/assets/img/laravel-logo.png)

同时也支持网址变量 ![RUNOOB][2]

[2]: https://laravel.com/assets/img/laravel-logo.png

使用HTML代码设置图片的长宽等
<img src="https://laravel.com/assets/img/laravel-logo.png" width="5%">

# 表格

| 表头   | 表头   |
| ------ | ------ |
| 单元格 | 单元格 |
| 单元格 | 单元格 |

对齐语法：
| 左对齐     |     右对齐 |  居中对齐  |
| :--------- | ---------: | :--------: |
| 单元格格格 | 单元格格格 | 单元格格格 |
| 单元格     |     单元格 |   单元格   |

# HTML用法

使用 <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>A</kbd> 截图

# 转义

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

# 公式
$$
\mathbf{V}_1 \times \mathbf{V}_2 =  \begin{vmatrix} 
\mathbf{i} & \mathbf{j} & \mathbf{k} \\
\frac{\partial X}{\partial u} &  \frac{\partial Y}{\partial u} & 0 \\
\frac{\partial X}{\partial v} &  \frac{\partial Y}{\partial v} & 0 \\
\end{vmatrix}
$$

# 选项
- [ ] 未完成
- [ ] 未完成
- [x] 已完成
- [x] 已完成
- [x] 已完成