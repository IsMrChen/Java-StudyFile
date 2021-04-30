# CSS学习笔记

参考链接：[CSS教程](https://www.runoob.com/css/css-background.html)

## 一、CSS背景设置

CSS 属性定义背景效果:

- background-color 
- background-image
- background-repeat
- background-attachment
- background-position

**background-color :** 设置背景颜色

```css
十六进制：#ff0000
RGB: rgb(255,0,0)
颜色名称：red
```

**background-image:** 设置背景图像

```css
body {
    background-image:url('img_tree.gif');
}
```

![image-20210430142900818](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210430142900818.png)

**background-repeat:** 设置图像水平平铺、垂直平铺、不平铺

在水平方向平铺：

```css
body
{
    background-image:url('img_tree.png');
    background-repeat:repeat-x;
}
```

![image-20210430142820404](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210430142820404.png)

在竖直方向平铺：

```css
body
{
    background-image:url('img_tree.png');
    background-repeat:repeat-y;
}
```

![image-20210430143154528](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210430143154528.png)

设置不平铺：

```css
body
{
    background-image:url('img_tree.png');
    background-repeat:no-repeat;
}
```

![image-20210430142747541](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210430142747541.png)

**background-position:** 设置定位

```css
body
{
    background-image:url('img_tree.png');
    background-repeat:no-repeat;
    background-position:right top;
}
```

![image-20210430143511976](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210430143511976.png)

背景-简写属性

我们可以把上述的背景设置合并在一个属性中，简称：background

```css
body {
    background:#ffffff url('img_tree.png') no-repeat right top;
}
```

当使用简写属性时，属性值的顺序为：

- background-color
- background-image
- background-repeat
- background-attachment
- background-position

以上属性无需全部使用，你可以按照页面的实际需要使用

<p style="color:red">background-attachment</p>

这个背景属性很特殊，因此单独提出来：

它是决定背景图像的位置是在视口内固定，或者随着包含它的区块滚动

```css
/* 关键 属性值 */
background-attachment: scroll;
background-attachment: fixed;
background-attachment: local;

/* 全局 属性值 */
background-attachment: inherit;
background-attachment: initial;
background-attachment: unset;
```

演示地址：[https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-attachment](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-attachment)

fixed:

此关键属性值表示背景相对于视口固定。即使一个元素拥有滚动机制，背景也不会随着元素的内容滚动。

local:

此关键属性值表示背景相对于元素的内容固定。如果一个元素拥有滚动机制，背景将会随着元素的内容滚动， 并且背景的绘制区域和定位区域是相对于可滚动的区域而不是包含他们的边框。

scroll:

此关键属性值表示背景相对于元素本身固定， 而不是随着它的内容滚动（对元素边框是有效的）

**多背景支持：**

此属性支持多张背景图片。你可以用逗号分隔来为每一张背景图片指定不同的`<attachment>属性值。`每一张背景图片顺序对应相应的 attachment 属性

```css
p {
  background-image: url("https://mdn.mozillademos.org/files/12057/starsolid.gif"),
      url("https://mdn.mozillademos.org/files/12059/startransparent.gif");
  background-attachment: fixed, scroll;
  background-repeat: no-repeat, repeat-y;
}
```

