<template>
  <div class="shopCart">
    <div class="shop-content" @click="toggleList">
      <div class="shop-content-left">
        <div class="shop-logo-wrapper">
          <div class="shop-logo" :class="{'highlight':totalCount>0}">
            <img width="25" height="25" src="../../assets/shop-cart-logo.png" alt="">
          </div>
          <div class="shop-num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="shop-price" :class="{'highlight':totalPrice>0}">¥{{totalPrice}}</div>
        <div class="shop-desc">另需配送费¥{{deliveryPrice}}元</div>
      </div>
      <div class="shop-content-right" @click.stop.prevent="payMoney">
        <div class="shop-content-pay" :class="payClass">
          <span>{{payDesc}}</span>
        </div>
      </div>
    </div>
    <!--小球-->
    <div class="ball-container">
        <div v-for="ball in balls">
          <transition v-on:before-enter="beforeEnter"
                      v-on:enter="enter"
                      v-on:after-enter="afterEnter">
            <div v-show="ball.show" class="ball">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
    </div>

    <transition name="folds">
      <div class="shopcart-list" v-show="listShow">
        <div class="shopcart-list-header">
          <h1 class="shopcart-list-header-title">购物车</h1>
          <span class="shopcart-list-header-empty" @click="shopcartListEmpty">清空</span>
        </div>
        <div class="shopcart-list-content" ref="shopcartListContent">
          <ul>
            <li class="list-food" v-for="food in selectFoods">
              <span class="list-food-name">{{food.name}}</span>
              <div class="list-food-price">
                <span>¥{{food.price*food.count}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food="food"></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </transition>
    <transition name="fades">
      <div class="shop-list-mask" @click="hideShow" v-show="listShow"></div>
    </transition>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  import cartcontrol from '../cartcontrol/cartcontrol'

  export default{
    components:{
      cartcontrol
    },

    props:{
      selectFoods:{
        type:Array,
        default(){
          return[
            {
              price:0,
              count:0
            }
          ];
        }
      },
      deliveryPrice:{
        type:Number,
        default:0
      },
      minPrice:{
        type:Number,
        default:0
      }
    },
    data(){
      return {
        //小球
        balls:[
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          },
          {
            show:false
          },
        ],
        dropBalls:[],
        fold:true
      }
    },
    methods:{

      //小球
      drop(el){
//        console.log(el)
        for (let i=0;i<this.balls.length;i++){
          let ball = this.balls[i];
          if(!ball.show){
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      beforeEnter(el){
        let ballCount = this.balls.length;
        while (ballCount--){
          let ball = this.balls[ballCount];
          if(ball.show){
            let rect = ball.el.getBoundingClientRect();
            let x = rect.left - 32;
            let y = -(window.innerHeight - rect.top - 22);
            el.style.display = '';
//              el.style.webkitTransform = `translate3d(0,${y}px,0)`;
            el.style.webkitTransform = 'translate3d(0,'+y+'px,0)';
            el.style.transform = 'translate3d(0,'+y+'px,0)';
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = 'translate3d('+x+'px,0,0)';
            inner.style.transform = 'translate3d('+x+'px,0,0)';
          }
        }
      },
      enter(el){
        /* eslint-disable no-unused-vars */
        let rf =el.offsetHeight;
        this.$nextTick(() => {
          el.style.webkitTransform = 'translate3d(0,0,0)';
          el.style.transform = 'translate3d(0,0,0)';
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = 'translate3d(0,0,0)';
          inner.style.transform = 'translate3d(0,0,0)';
        })
      },
      afterEnter(el){
        let ball = this.dropBalls.shift();
        if(ball){
          ball.show = false;
          el.style.display = 'none';
        }
      },
      toggleList(){
        if(!this.totalCount){
          return;
        }
        this.fold = !this.fold;
//        console.log(this.fold)
      },
      hideShow(){
        this.fold = true
      },
      shopcartListEmpty(){
        this.selectFoods.forEach((food) => {
          food.count = 0;
        })
      },
      payMoney(){
        if(this.totalPrice < this.minPrice){
          return;
        }
        let pay = window.confirm('支付'+this.totalPrice+'元');
        if(pay){
          window.alert('支付成功！')
        }
        else {
          window.alert('支付失败！')
        }
      }
    },

    computed:{
      totalPrice(){
        let total = 0;
        this.selectFoods.forEach((food) => {
            total += food.price * food.count;
        });
        return total;
      },
      totalCount(){
        let count = 0;
        this.selectFoods.forEach((food) => {
           count += food.count;
        });
        return count;
      },
      payDesc(){
        if(this.totalPrice === 0){
//        return '¥'+this.minPrice+'元起送';
          return `¥${this.minPrice}元起送`;
        }else if(this.totalPrice < this.minPrice){
          let diff = this.minPrice - this.totalPrice;
          return `还差¥${diff}元起送`;
        }else {
            return '去结算';
        }
      },
      payClass(){
          if(this.totalPrice < this.minPrice){
              return 'not-enough';
          }else {
            return 'enough';
          }
      },
      listShow(){
        if(!this.totalCount){
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        if(show){
          this.$nextTick(() => {
            if(!this.scroll){
              this.scroll = new BScroll(this.$refs.shopcartListContent,{
                click:true
              });
            }else{
              this.scroll.refresh();
            }
          })
        }
        return show;
      }
    }
  }
</script>

<style scoped>
  .shopCart{
    position: fixed;
    left: 0;
    bottom: 0;
    z-index: 40;
    width: 100%;
    height: 48px;
  }
  .shop-content{
    display: flex;
    background: #141d27;
    font-size: 0;
  }
  .shop-content-left{
    flex: 1;
  }
  .shop-logo-wrapper{
    display: inline-block;
    position: relative;
    top: -10px;
    margin: 0 12px;
    padding: 6px;
    width: 56px;
    height: 56px;
    box-sizing: border-box;
    text-align: center;
    background: #141d27;
    border-radius: 50%;
    vertical-align: top;
  }
  .shop-logo{
    width: 100%;
    height: 100%;
    background: #2b343c;
    border-radius: 50%;
  }
  .shop-logo.highlight{
    background: rgb(10,160,220);
  }
  .shop-logo img{
    margin-top: 10px;
  }
  .shop-num{
    position: absolute;
    top:0;
    right: 0;
    width: 24px;
    height: 16px;
    line-height: 16px;
    text-align: center;
    border-radius: 16px;
    font-size: 9px;
    font-weight: bold;
    color: #fff;
    background: rgb(240,20,20);
    box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4);
  }
  .shop-price{
    display: inline-block;
    vertical-align: top;
    margin-top: 12px;
    line-height: 24px;
    padding-right: 12px;
    box-sizing: border-box;
    border-right: 1px solid rgba(255,255,255,0.1);
    font-size: 16px;
    font-weight: bold;
    color: rgba(255,255,255,0.4);
  }
  .shop-price.highlight{
    color: #fff;
  }
  .shop-desc{
    display: inline-block;
    vertical-align: top;
    margin: 12px 0 0 12px;
    line-height: 24px;
    font-size: 10px;
    color: rgba(255,255,255,0.4);
  }
  .shop-content-right{
    flex: 0 0 105px;
    width: 105px;
    background: #2b333b;
  }
  .shop-content-pay{
    height: 48px;
    line-height: 48px;
    text-align: center;
    font-size: 12px;
    color: rgba(255,255,255,0.4);
    font-weight: bold;
  }
  .shop-content-pay.not-enough{
    background: #2b333b;
  }
  .shop-content-pay.enough{
    color: #fff;
    background: #00b43c;
  }
  .shopcart-list{
    position: absolute;
    top:0;
    left:0;
    z-index: -1;
    width: 100%;
    transform: translate3d(0,-100%,0);
  }
  .folds-enter,.folds-leave-to{
    transform: translate3d(0,0,0);
  }
  .folds-enter-active,.folds-leave-active{
    transition: all 0.5s;
  }
  .folds-enter-to,.folds-leave{
    transform: translate3d(0,-100%,0);
  }
  .shopcart-list-header{
    height: 40px;
    line-height: 40px;
    padding: 0 18px;
    background: #f3f5f7;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .shopcart-list-header-title{
    float: left;
    color: rgb(7,17,27);
  }
  .shopcart-list-header-empty{
    float: right;
    font-size: 12px;
    color: rgb(0,160,220);
  }
  .shopcart-list-content{
    padding: 0 18px;
    max-height: 217px;
    overflow: hidden;
    background: #fff;
  }
  .list-food{
    position: relative;
    padding: 12px 0;
    box-sizing: border-box;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .list-food:last-of-type{
    border-bottom:none;
  }
  .list-food-name{
    line-height: 24px;
    color: rgb(7,17,27);
  }
  .list-food-price{
    position: absolute;
    right: 90px;
    bottom: 12px;
    line-height: 24px;
    color: rgb(240,20,20);
    font-weight: bold;
  }

  .shop-list-mask{
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -2;
    background: rgba(7,17,27,0.6);
    transition: all 0.5s;
  }
  .fades-enter,.fades-leave-to{
    opacity: 0;
  }
  .fades-enter-to,.fades-leave{
    opacity: 1;
  }

  /*小球*/
  .ball{
    position: fixed;
    left: 32px;
    bottom: 22px;
    z-index: 50;
    transition: all 0.5s cubic-bezier(0.59,-0.1,0.75,0.41);
  }
  .inner{
    width: 16px;
    height: 16px;
    border-radius: 50%;
    background: rgb(0,160,220);
    transition: all 0.5s linear;
  }
</style>
