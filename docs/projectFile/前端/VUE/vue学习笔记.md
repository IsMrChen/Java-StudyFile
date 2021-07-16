## 1. vue 动态实现select选择后获取被选择行的数据

1.实现原理就是select绑定change事件，然后在change绑定的时间方法中，遍历option对应的数据集与被选择的数据id进行比较，选出id一样的option即可/

2.首先定义option的数据集

```javascript
return{
    ...
    disposeOptions: [],
}
```

3.template详情

```html
<el-select
            v-model="createForm.disposeCode"
            filterable
            placeholder="请选择"
            @change="selectDisposeCode"
          >
            <el-option
              v-for="item in disposeOptions"
              :key="item.id"
              :label="item.code"
              :value="item.id"
            >
            </el-option>
</el-select>
```

4.编写select的change事件对应的方法

```javascript
selectDisposeCode(oId) {
      let obj = {};
      console.log(this.disposeOptions);
      obj = this.disposeOptions.find((item) => {
        return item.id === oId;
      });
      //这里获取的obj就是选择的那一整条数据
      //我这里实现的业务是选择完disposeCode后给disposeName赋值
      this.createForm.disposeName = obj.name;
    },
```