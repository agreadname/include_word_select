## inclue_word_select
使用手册

  使用手册：
  父组件：
  ###  1. 导入组件，url为组件地址
     import myselect from 'url' 
  ###  2.注册组件
    components: {
        myselect
      }
  ### 3.给组件传递参数

  属性名  | 默认值  | 数据类型 | 作用
 ---- | ----- | ------  | ------  
  options  | - | Object|选项数据，数据格式见data.js 
 firstdt  |全部车型 |String| 二级菜单标题
 seconddt  |全部车系 |String| 三级菜单标题
 v-model|-|[]|Array|选中项返回值

### 4.事件
 
 事件名  | 默认值  | 回调 | 作用
 ---- | ----- | ------  | ------
 change| - | function(value){} |value值为选中后返回的值，该函数用于选择完毕之后
  
 ### 实例
  ` <myselect :options=data firstdt='全部车系' v-model="selectvalue" @change="handle"></myselect>`

#### options
`{
    'index':['A','B'],//导航栏的字符串，需要和下面的data对应
    'data':{
      'A':{
        value:'',//一级
        label:'',
        children:[
          {
            value:'',//二级
            label:''
          },
            {
            value:'',
            label:'',
            children:[]//三级
          },
            {
            value:'',
            label:''
          },
        ]
      }
    }
  }
 `
