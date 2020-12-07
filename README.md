# 林大课表

## 项目初始化

```shell
npm install
```

安装相关依赖。

## 主要代码

除了`App.vue`之外，有三个组件:

![CustomHeader.vue](https://i.loli.net/2020/12/07/hW1Ykw4sTO7U2RD.png)

其中`CustomHeader.vue`是纯样式的组件。

![InputInfo.vue](https://i.loli.net/2020/12/07/I1KEDLfbNplYRzQ.png)

`InputInfo.vue`是输入框。

![CourseTable.vue](https://i.loli.net/2020/12/07/H8d4WNDn6ElGStP.png)

`CourseTable.vue`则是真正的课表。



当用户正确输入并点击`CustomHeader.vue`中的查询按钮时，触发点击事件，向父组件传值:

```javascript
	  /*
       *查询按钮点击事件，将用户输入的年级，专业，班级传给主组件App.vue
       */
      searchBtn() {
          this.$emit('onSearch',this.inputGrade,this.inputMajor,this.inputClass);
      }
```

`App.vue`中用`axios`在本地`json`文件夹中寻找文件，若匹配，则将找到的json文件解析为`classTable`对象，通过`props`的方式传递给子组件`CourseTable.vue` 。

对于课表的生成，采取的思路是记录下当前选择的周数`selectWeek`，在`watch{}`中监视`selectWeek`的变化，并根据`selectWeek`的变化实时生成   `Map`对象   `WekTable` 。`WekTable`是本周课表的映射。映射关系为 `String` -> `Object` 。