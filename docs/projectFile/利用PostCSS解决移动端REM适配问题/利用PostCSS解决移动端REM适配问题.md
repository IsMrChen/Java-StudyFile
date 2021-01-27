移动端适配问题用到的插件：`postcss-pxtorem`

主要功能是将px根据手机型号自动适配成rem

设置这个插件以后，所有的px单位都会自动适配成rem



在这个过程中，也会有一些问题产生：

例如，边框为1px的设置我们并不希望它的单位变成rem

再例如最近的项目中遇到的一个问题，因为手机适配原因，导致了某控件有误差

![](img\适配问题1.png)

通过下面这张图片可以看到它将单位强转为rem

在滑动触发的时候就出现了问题

![1](img\1.png)

解决办法就是要配置下这个插件的CSS过滤，让它忽略掉这个组件的样式转换，也就是让它始终为px单位（固定值）



要想过滤，就需要了解这个控件的配置使用    [参考文献](https://segmentfault.com/a/1190000010947054)

```js
require('postcss-pxtorem')({
  rootValue: 75, //是对根元素大小进行设置 如果设计稿为750px 那么该处就填写750
  unitPrecision: 5, //转换成rem后保留的小数点位数
  propList: ['*'],
  selectorBlackList: [],
  replace: true,
  mediaQuery: false,
  minPixelValue: 12
})
```

**propList**是一个存储哪些将被转换的属性列表，这里设置为`['*']`全部，假设需要仅对边框进行设置，可以写`['*', '!border*']`意思是排除带有border的属性，当然这里会有一个问题，也许有时候不想对border其他样式处理例如`border-radius`所以也不是很好

**selectorBlackList**则是一个对css选择器进行过滤的数组，比如你设置为`['fs']`，那例如`fs-xl`类名，里面有关px的样式将不被转换，这里也支持正则写法

**minPixelValue**是一个非常不错的选项，意思是所有小于12px的样式都不被转换，那么border之类的属性自然会保留px值了

了解这么多，我们可以看到  selectorBlackList: []这个属性的配置就是这个文件解决的关键

也就是需要找到因适配出现问题的组件类名，然后配置进去，就可以了

通过上图可以看到拾取器相关的组件类名是`nut-picker`

因此我们配置如下

```js
selectorBlackList['nut-picker']
```

项目的文件：

```js
module.exports = {
  plugins: [
    require('postcss-pxtorem')({
      rootValue: 37.5, // 设计稿为375px
      //selectorBlackList: ['nut'], // 忽略转换正则匹配项
      minPixelValue: 2, // 2像素以下的不进行适配
      propList: ['*'], // 要转换的匹配项
      selectorBlackList:['.nut-picker']//对css选择器进行过滤的数组，比如你设置为['fs']，那例如fs-xl类名，里面有关px的样式将不被转换，这里也支持正则写法
    })
  ]
}
```

问题解决！

![2](img\2.png)























