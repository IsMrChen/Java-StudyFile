## 1 简单图标😀

❌✔️😀

## 2 文字颜色<font color=green>颜色</font>设置

```html
<font color=green>SpringBoot</font>
```

效果：<font color=green>SpringBoot</font>

## 3 引入外部链接图片并居中

```
<p align="center">
<img src="https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2481424715,2807309609&fm=26&gp=0.jpg" width="200" height="200"/>
</p>
```

效果：

<p align="center">
<img src="https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2481424715,2807309609&fm=26&gp=0.jpg" width="200" height="200"/>
</p>


## 4 Markdown 文档中使用 <font color=red>Font Awesome</font>

<font color=red>准备工作：</font>在index.html文件中添加如下配置

```
<script defer src="https://use.fontawesome.com/releases/v5.0.13/js/all.js"></script> 
<script defer src="https://use.fontawesome.com/releases/v5.0.13/js/v4-shims.js"></script> 

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.0.13/css/all.css">
```

![image-20210721182701280](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210721182701280.png)

![image-20210721182723175](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210721182723175.png)

在官网搜索你想用的图标：

[Font Awesome](https://fontawesome.com/v5.15/icons?d=gallery&p=2&m=free)

![image-20210721182837540](https://gitee.com/JongcyChen/PicBed/raw/master/img/image-20210721182837540.png)



添加到你文档内部想用的地方

```
<i class="far fa-calendar-alt fa-2x"></i>
<i class="far fa-【用图标的名称替换即可】 fa-2x"></i>
同样，你可以设置它的颜色 这个网站的图标非常多，完全够用了！
<i class="far fa-calendar-alt fa-2x" style="color:red"></i>
```

"fa-2x"表示两倍，去掉则为正常。

效果图

<i class="far fa-calendar-alt fa-2x" style="color:red"></i>

<i class="far fa-calendar-alt"></i>