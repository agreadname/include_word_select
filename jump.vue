<template>
   <div class="find-car" @click="output($event)">
       <!-- 给的数据 -->
       <!-- find-car-select-->
       <!-- <div>{{msg}}</div> -->
       <!-- <input type="text" v-model="model"> -->

       <div class="find-car-select">
           <!-- placeholder为选中后要替换的文字，默认只显示最后的字符-->
           <div :class="{'find-car-select-title':true,'pc333':placeholder!=='请选择'}" @click="handleInput">
               {{placeholder}}         
           </div>
            <!--indexdata为旁边的导航栏-->
           <div class="find-car-box" v-if="selectbox">
                <ol class="index"  @click="handleOlClick">
                    <li v-for="(word,index) in indexdata" :key="index">{{word}}</li>
                </ol>
                <!-- 一级 -->
                <dl class="find-car-content" v-html="dtdd" @click="handleddClick">
                </dl> 
           </div>
            <!-- 二级 -->
            <div class="find-car-box car-type-list" v-if="Next2">
                <dl @click="handleSecond">
                    <dt>{{firstdt}}</dt>
                    <dd v-for="(item,index) in carType" :data-context=index :key=index>{{item.label}}</dd>
                </dl>
            </div>
            <!-- 三级 -->
             <div class="find-car-box car-suit-list" v-if="Next3">
                <dl @click="handleThird">
                    <dt>{{seconddt}}</dt>
                    <dd v-for="(item,index) in carSiere" :data-context=index :key=index>{{item.label}}</dd>
                </dl>
             </div>
       </div>
   </div>
</template>
<script>

/*
    需求：
    按照三级写,最多三级
    1 可以选择最后一级，也可以选择任意一级，默认要选择到最后一级 这个有点麻烦，最好是到最后一级
 done   2 某些数据可能为一级二级三级，加个children的判断，
       加个判断 没有下一极则代表：children为undefined，null，[]
 done   3 一级比较特殊，有字母导航，二级三级没有，则可以用v-html来写
 done   4 功能
   ***必须传入的参数：index，赋值给indexdata，用于左边侧边栏
   ***必须传入的参数：data,在dtdd处理
   ***returnV为返回值
   ***考虑异步
   =============目前已实现功能================
    props:
    options:{}//选择框的数据
    ============未解决=============
    https://www.cnblogs.com/gsgs/p/7294160.html
    参考网址
   done 选择完毕后把returnV值返回给父组件，父组件可以进行处理
   done 父组件可以写@change函数，有个回调参数
    https://api.myjson.com/bins/fulds
*/

export default {

  data() {
    return {
      returnV: [], // 用于保存选择后返回值的,即v-model的值
      Next2: false, // 二级控制是否显示，默认隐藏
      Next3: false, // 三级控制是否显示，默认隐藏
      selectbox: false,
      data: {}, // 接收options的数据
      dtdd: '', // 一级菜单的数据
      placeholder: '请选择',
      indexdata: [], // 侧边栏的导航数据
      carType: [], // 二级菜单的数据
      carSiere: [], // 三级菜单的数据
      isClose: false, // 判断是否在本选择框里面
      isLast: false // 是否选中到最后
    }
  },
  mounted() {
    this.data = this.options
    this.handleFirst()
    document.addEventListener('click', function(e) {
      // var e = e || event
      this.isClose = false
      // this.finishedSelect()
      console.log('d输出的' + this.isClose)
    })
  },
  methods: {
    output(event) {
      this.isClose = true
      console.log('o输出的' + this.isClose)
    },
      // 处理输入框
    handleInput() {
      this.mm = false
      this.selectbox = !this.selectbox
      this.Next2 = false
      this.Next3 = false
        // 处理可能异步的重新调用一次
      if (!(this.data === this.options)) {
        this.data = this.options
        this.handleFirst()
      }
      this.returnV = []
    },
      // 一级数据遍历
    handleFirst() {
         // 导航栏
      this.indexdata = this.data.index
      for (var key in this.data.data) {
        this.dtdd += `<dt data-word=${key}>${key}</dt>`
        for (var i = 0; i < this.data.data[key].length; i++) {
          var val = this.data.data[key][i]
          this.dtdd += `<dd  data-context=${i} data-key=${key}>${val.label}</dd>`
        }
      }
    },
    // 处理一级点击事件，调出二级菜单,事件委托，减少事件流
    handleddClick(e) {
      if (e.target.nodeName.toLowerCase() === 'dd') {
        const index = parseInt(e.target.dataset.context)
        const key = e.target.dataset.key
        const item = this.data.data[key][index]
        this.returnV[0] = this.clearChild(item)
        this.carType = this.handledata(item, 1)
      }
    },
    clearChild(item) {
      var pushV = {}
      for (var k in item) {
        if (k !== 'children') {
          pushV[k] = item[k]
        }
      }
      return pushV
    },
    // 处理数据遍历,item为要处理的数据，level为等级
    handledata(item, level) {
      if (item.children === undefined || item.children === [] || item.children === null) {
        this.placeholder = item.label
        this.Nochild(level)
        this.finishedSelect()
        return []
      } else { // carType
        if (level === 1) { this.Next2 = true }
        if (level === 2) {
          this.Next2 = true
          this.Next3 = true
        }
        var res = item.children
        return res
      }
    },
    // 处理二级点击事件与三级数据遍历
    handleSecond(e) {
      if (e.target.nodeName.toLowerCase() === 'dd') {
        const index = parseInt(e.target.dataset.context)
        const item = this.carType[index]
        this.carSiere = this.handledata(item, 2)
        this.returnV[1] = this.clearChild(item)
      }
    },
    // 处理三级点击事件
    handleThird(e) {
      if (e.target.nodeName.toLowerCase() === 'dd') {
        const index = parseInt(e.target.dataset.context)
        const item = this.carSiere[index]
        this.placeholder = item.label
        this.returnV[2] = this.clearChild(item)
        this.finishedSelect()
      }
    },
    finishedSelect() { // 已选择完毕，全部调用关闭
      this.Next2 = false
      this.Next3 = false
      this.selectbox = false
      this.mm = true
    },
    Nochild(val) { // 1:二三级选择框关闭,2：三级选择框关闭
      if (val === 1) {
        this.Next2 = false
        this.Next3 = false
      }
      if (val === 2) {
        this.Next3 = false
      }
    },
    handleOlClick(e) {
        // offsetTop是元素的上边框与父元素的上边框的绝对距离
        // scrollTop是只某个课滚动区块向下滚动的距离
        // clientHeight视口大小
        // 事件委托性能优化，减少事件流
      if (e.target.nodeName.toLowerCase() === 'li') {
        var c = e.target.innerHTML
        var a = document.querySelector('[data-word=' + c + ']')
        var b = document.querySelector('dl')
        var lastdd = document.querySelector('dl>dd:last-child')
        var stop = lastdd.offsetTop - b.clientHeight
        const timer = setInterval(function() {
          var dance = b.scrollTop - a.offsetTop
          if (dance <= 0) { // 下滚
            b.scrollTop += 20
          } else { // 上滚
            b.scrollTop -= 20
          }
        // 处理已经滑到底端
          if (b.scrollTop >= stop) {
            b.scrollTop = stop
            clearInterval(timer)
            return
          }
        // 处理当距离小于25时的情况
          if (Math.abs(dance) <= 25) {
            b.scrollTop = a.offsetTop
            clearInterval(timer)
            return
          }
        }, 10)
      }
    }
  },
  props: {
    options: [Object],
    firstdt: {
      default: '全部车型'
    },
    seconddt: {
      default: '全部车款'
    }

  },
  created() {

  },
  computed: {
    mm: {// 当mm为true的时候则执行回调函数和会调参数
      get() {
        return this.mm
      },
      set(val) {
        if (val) {
          this.$emit('input', this.returnV)
          this.$emit('change', this.returnV)
        }
      }
    }
  }

}
</script>
<style>
        .find-car-box dt{
                    border-top:1px solid #ddd;
                    border-bottom:1px solid #ddd;
                    background: #eee;
                    padding-left: 10px;
                    
                }
        .find-car-box dd{
                    cursor: pointer;
                     padding-left: 15px;
                }
               
</style>

<style lang="scss" scoped>
//一级的用width:100%;二级的60%，
.find-car{
    width: 100%;
    .find-car-select{
        position: relative;
        width: 100%;
        z-index: 1;
        border: 1px solid #ccc;
        height: 40px;
        margin-right: 10px;
        .find-car-select-title{
            padding: 0 20px 0 10px;
            cursor: pointer;
            color: #999;
            white-space: nowrap;
            overflow: hidden;
            height: 100%;
            line-height: 40px;
        }
        .find-car-select-title:after{
            content:'';
            position: absolute;
            right: 9px;
            top: 16px;
             width: 0;
            height: 0;
            border-left: 8px solid transparent;  
            border-right: 8px solid transparent;
            border-top: 6px solid #aaa;
        }
         .pc333{
                    color: #333;
                }
        .find-car-box{
            display:block;
            position: absolute;
            left: -1px;
            top: 40px;
            width: 100%;
            max-height: 473px;
            border: 1px solid #ccc;
            overflow: hidden;
            line-height: 32px;
            color: #333;
            background-color: #fff;
            .find-car-content{
                position: relative;
              
            }
            .index {
                position: absolute;
                left: 0;
                top: 0;
                height: 100%;
                width: 30px;
                font-size: 12px;
                line-height: 20px;
                text-align: center;
                color: #fff;
                background-color: #00142A;
                li{
                    cursor: pointer;
                }
            }
            dl{
                margin-left: 30px;
                max-height: 471px;
                overflow-y: scroll;
              

            }
             
           
        }
        .car-type-list{
            width:60%;
            left:38%;
            dl{
                margin-left: 0px;
            }
        }
        .car-suit-list{
            width:60%;
            left:90%;
            dl{
                margin-left: 0px;
            } 
        }
    }
     
}
.find-car ::-webkit-scrollbar-thumb{
        background-color: #001f3f;        
            }
.find-car ::-webkit-scrollbar-track-piece{
                background-color: #eee;
            }
   
</style>


