<template>
<!-- 实验品 -->
  <div id="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <div v-for="(item,index) in goods" :key = 'index' class="menu-item" :class='{current:currentIndex === index}' @click ='selectIndex(index,$event)'>
        <div class="text-wrapper">
          <span v-show="item.type > 0" :class="menuMap[item.type]" class="icon"></span>
          <span class="item-text">{{item.name}}</span>
        </div>
        
      </div>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="(item,index) in goods" :key="index" class="foods-list">
          <div class="item-title">{{item.name}}</div>
          <div v-for="(item2,index) in item.foods" :key='index' class="food">
            <div class="food-img">
              <img :src="item2.image" alt="">
            </div>
            <div class="food-content">
              <div class="content-title">{{item2.name}}</div>
              <div class="content-description">{{item2.description}}</div>
              <div class="content-sell">
                <span>月售{{item2.sellCount}}份</span>
                <span>好评率{{item2.rating}}%</span>
              </div>
              <div class="price">
                <span class="now"><span class="money">￥</span>{{item2.price}}</span>
                <span class="old" v-show="item2.oldPrice">￥{{item2.oldPrice}}</span>
              </div>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Vue from 'vue'
import betterScroll from 'better-scroll'
export default {
  name: 'goods',
  data () {
    return {
      goods: [],
      menuMap:['decrease','discount','special','invoice','guarantee'],
      heightList:[],
      scrollY : 0
    }
  },
 
  created(){
    axios.get('/good/goods').then(
      res => {
        if(res.data.code === 0) {
          this.goods = res.data.data
          console.log(this.goods)
          Vue.nextTick(() =>{
            //给dom 绑定一个scroll
            //计算每一个foods 的高度
            this.initscroll()
            this.calculateHeight()
          })
        }
      }
    )
  },
   methods:{
    selectIndex(index,$event){
      console.log(index,$event)
      if(!$event._constructed){
        return;
      }
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('foods-list')
      // this.foodScroll.scrollTo(0,-400)
      
          /*通过高度滚动*/
    
      // let Y = this.heightList[index]
      // console.log(Y)
      // this.foodScroll.scrollTo(0,-Y)


      /*滚动到元素位置*/ 
      console.log(foodList[index])
      this.foodScroll.scrollToElement(foodList[index], 300)
    },
    //better-scroll
    initscroll(){
      this.menuScroll = new betterScroll(this.$refs.menuWrapper,{
        click:true    // 派生一个点击事件   而且better-scroll 会默认阻止浏览器的原生click事件
      })
      this.foodScroll = new betterScroll(this.$refs.foodsWrapper,{
        probeType : 3,   //0不触发scroll事件 1 滑动超过一定时间触发  2 滑动触发  3 2的基础上momentum滚动动画也会触发
        click : true
      })
      this.foodScroll.on("scroll",pos =>{
        console.log(pos)
        this.scrollY = Math.abs(Math.round(pos.y))
      })
      //  this.foodScroll.scrollTo(0,-400)
    },
    calculateHeight(){
      let foodList = this.$refs.foodsWrapper.getElementsByClassName("foods-list");
      let height = 0;
      // console.log(foodList)
      for(let i = 0 ; i < foodList.length ; i ++) {
        let item = foodList[i]
        height += item.clientHeight;
        this.heightList.push(height)
      }
      console.log(this.heightList)
      console.log(this.currentIndex)
    }
  },
  computed : {
    currentIndex(){
      for(let i = 0 ; i < this.heightList.length; i ++){
        let height1 = this.heightList[i]
        let height2 = this.heightList[i + 1]
        if(!height2 || (this.scrollY <= height1)){
          return i
        }
      }
      return 0;
    }
  }
 
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>


</style>
<style lang="stylus" scoped>
@import '../../static/style.css';
@import '../assets/stylus/index.styl';
  #goods
    border-top 1px solid rgba(7,17,27,0.1)
    display flex
    position absolute
    top 174px 
    bottom 48px
    left 0 
    right 0
    font-size 0
    overflow hidden
    .menu-wrapper
      flex 0 0 80px
      width 80px
      background-color #f3f5f7
      .menu-item
        padding  0 12px
        &.current
          background-color #ffffff
          position relative
          top -1px
          &:after 
            border-top 1px solid #fff
          .text-wrapper
            border-1px(#fff)
            .item-text
              font-size 12px
              color rgb(7,17,27)
              font-weight 500
              line-height 14px
        .text-wrapper
          display table-cell
          width 56px
          height 54px
          border-1px(rgba(7,17,27,.1))
          vertical-align middle
          .item-text
            font-size 12px
            color rgb(7,17,27)
            font-weight 200
            line-height 14px
         
          .icon
            display inline-block
            vertical-align top
            width 14px 
            height 14px
            background-size 14px 14px
            &.decrease
              bg-image('decrease_3')
            &.discount
              bg-image('discount_3')
            &.special
              bg-image('special_3')
            &.invoice
              bg-image('invoice_3')
            &.guarantee
              bg-image('guarantee_3')
              

    .foods-wrapper
      flex 1
      background-color #fff
      ul
        width 100%
        // height 100%
        .foods-list
          width 100%
          // height 100%
          .item-title
            border-left 1px solid #d9dde1
            padding-left 14px
            // display table-cell
            height  28px
            line-height 28px
            background-color rgb(#f3f5f7)
            color rgb(147,153,159)
            font-size 12px
          .food
            border-1px rgba(7,17,27,.1)
            margin 0 18px
            padding 18px 0
            display flex
            .food-img
              flex 0 0 60px
              img
                set-img(60px)
            .food-content
              flex 1
              padding-top 2px
              margin-left 10px
              .content-title
                color rgb(7,17,27)
                font-size 14px
                line-height 14px 
              .content-description
                margin-top 8px
                color rgb(147,153,159)
                font-size 10px
                line-height 10px
              .content-sell
                color rgb(147,153,159)
                margin-top 8px
                span 
                  font-size 10px
                  margin-right 6px
              .price
                margin-top 4px
                .now
                  color rgb(240,20,20)
                  font-size 14px
                  line-height 24px
                  font-weight 700
                  .money
                    font-size 10px
                .old
                  vertical-align top
                  color rgb(147,153,159)
                  text-decoration line-through
                  margin-left 8px
                  font-size 10px
                  font-weight 700
                  line-height 20px


</style>
