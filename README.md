# inclue_word_select
使用手册

  使用手册：
  父组件：
  #  1. 导入组件，url为组件地址
     import myselect from 'url' 
  #  2.注册组件
    components: {
        myselect
      }
  #  3.给组件传递参数

  属性名  | 默认值  | 数据类型 | 作用
 ---- | ----- | ------  | ------  
  options  | 单元格内容 | 单元格内容 
 单元格内容  | 单元格内容 | 单元格内容  

# 4.事件
 
 事件名  | 默认值  | 数据类型 | 作用
      ---- | ----- | ------  | ------  
  options  | 单元格内容 | 单元格内容 |
   接收菜单栏数据，数据格式如下 {}
    firstdt:二级菜单标题 ''
    seconddt:三级菜单标题 ''
    v-model="预设值的数组变量名" []
 # <myselect :options=data firstdt='全部车系' v-model="selectvalue" @change="handle"></myselect>

"# include_word_select" 
