<template>
  <transition name="left">
    <div v-show="showFlag" class="food" ref="food">
      <div class="food-wrapper">
        <div class="food-headerImg">
          <img :src="food.image" alt="">
          <span class="icon-arrow-left" @click="hideBack">&lt;</span>
        </div>
        <div class="food-content">
          <h2 class="food-title">{{food.name}}</h2>
          <div class="food-detail">
            <span class="food-sell-count">月售{{food.sellCount}}</span><span class="food-rating">好评率{{food.rating}}%</span>
          </div>
          <div class="food-price">
            <span class="food-price-now">¥{{food.price}}</span>
            <span class="food-price-old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :food="food" @cart-add="cartCril"></cartcontrol>
          </div>
          <div @click.stop.prevent="addBuy" class="food-buy" v-show="!food.count || food.count===0">加入购物车</div>
        </div>
        <split v-show="food.info"></split>
        <div class="food-info" v-show="food.info">
          <div class="food-info-title">商品介绍</div>
          <p class="food-info-text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="food-rating">
          <h2 class="food-rating-title">商品评价</h2>
          <ratingselect :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="food.ratings" @ratingSelect="ratingSelect" @ratingToggle="ratingToggle"></ratingselect>
          <div class="rating-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <li v-show="needShow(ratings.rateType,ratings.text)" v-for="ratings in food.ratings" class="rating-item">
                <div class="rating-user">
                  <span class="rating-user-name">{{ratings.username}}</span>
                  <img class="rating-user-avatar" width="12" height="12" :src="ratings.avatar" alt="">
                </div>
                <div class="rating-time">{{ratings.rateTime | formaDate}}</div>
                <p class="rating-text">
                  <span :class="{'icon-up-hands':ratings.rateType===0,'icon-down-hands':ratings.rateType===1}"></span>
                  {{ratings.text}}
                </p>
              </li>
            </ul>
            <div class="no-ratings" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  import BScroll from 'better-scroll'
  import Vue from 'vue'
  import cartcontrol from '../cartcontrol/cartcontrol'
  import split from '../split/split'
  import ratingselect from '../ratingselect/ratingselect'
  import {formaDate} from '../../common/js/date'

  const POSITIVE = 0;
  const NEGATIVE = 1;
  const ALL = 2;

  export default{
    components:{
      cartcontrol,
      split,
      ratingselect
    },
    props:{
      food:{
        type:Object
      }
    },
    data(){
      return{
        showFlag:false,
        selectType:ALL,
        onlyContent:true,
        desc:{
          all:'全部',
          positive:'推荐',
          negative:'吐槽'
        }
      }
    },
    filters:{
      formaDate(time){
        let date = new  Date(time);
        return formaDate(date,'yyyy-MM-dd hh:mm');
      }
    },
    methods:{
      cartCril(event){
        this.$emit('cart-add',event);
      },
      show(){
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = false;
        this.$nextTick(() => {
          if(!this.scroll){
            this.scroll = new BScroll(this.$refs.food,{
              click:true
            });
          }else{
            this.scroll.refresh();
          }
        });
      },
      hideBack(){
        this.showFlag = false;
      },
      addBuy(event){
        if(!event._constructed){
          return;
        }
        this.$emit('cart-add',event.target);
        Vue.set(this.food,'count',1)
      },
      ratingSelect(type){
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        })
      },
      ratingToggle(){
        this.onlyContent = !this.onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        })
      },
      needShow(type,text){
        if(this.onlyContent && !text){
            return false;
        }
        if(this.selectType === ALL){
            return true;
        }else{
          return type === this.selectType;
        }
      }
    }
  }
</script>

<style scoped>
  .food{
    position: fixed;
    left: 0;
    top: 0;
    bottom: 48px;
    z-index: 20;
    width: 100%;
    background: #fff;
    transition: all 0.3s linear;
  }
  .left-enter,.left-leave-to{
    transform: translateX(100%);
  }
  .left-enter-to,.left-leave{
    transform: translateX(0);
  }
  .food-headerImg{
    position: relative;
    width: 100%;
    height: 0;
    padding-top: 100%;
  }
  .food-headerImg img{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
  .icon-arrow-left{
    display: inline-block;
    position: absolute;
    top: 10px;
    left: 0;
    padding: 10px;
    font-size: 20px;
    color: #fff;
  }
  .food-content{
    position: relative;
    padding: 18px;
  }
  .food-title{
    line-height: 14px;
    font-weight: bold;
    color: rgb(7,14,27);
    margin-bottom: 8px;
  }
  .food-detail{
    margin-bottom: 18px;
    line-height: 10px;
  }
  .food-sell-count{
    margin-right: 10px;
  }
  .food-sell-count,.food-rating{
    font-size: 10px;
    color: rgb(147,153,159);
  }
  .food-price{
    font-weight: bold;
    line-height: 24px;
  }
  .food-price-now{
    margin-right: 8px;
    font-size: 14px;
    color: rgb(240,20,20);
  }
  .food-price-old{
    text-decoration: line-through;
    font-size: 10px;
    color: rgb(147,153,159);
  }
  .cartcontrol-wrapper{
    position: absolute;
    right: 18px;
    bottom: 18px;
  }
  .food-buy{
    position: absolute;
    right: 18px;
    bottom: 18px;
    z-index: 10;
    height: 24px;
    line-height: 24px;
    padding: 0 12px;
    box-sizing: border-box;
    font-size: 10px;
    border-radius: 12px;
    color: #fff;
    background: rgb(0,130,220);
  }
  .food-info{
    padding: 18px;
  }
  .food-info-title{
    line-height: 14px;
    margin-bottom: 6px;
    color: rgb(7,17,27);
  }
  .food-info-text{
    line-height: 24px;
    padding: 0 8px;
    font-size: 12px;
    color: rgb(77,85,93);
  }
  .food-rating{
    padding-top: 18px;
  }
  .food-rating-title{
    line-height: 14px;
    margin-left: 18px;
    color: rgb(7,17,27);
  }
  .rating-wrapper{
    padding: 0 18px;
  }
  .rating-item{
    position: relative;
    padding: 16px 0;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .rating-user{
    position: absolute;
    right: 0;
    top: 16px;
    line-height: 12px;
  }
  .rating-user-name{
    display: inline-block;
    margin-right: 6px;
    vertical-align: top;
    font-size: 10px;
    color: rgb(147,153,159);
  }
  .rating-user-avatar{
    border-radius: 50%;
  }
  .rating-time{
    margin-bottom: 6px;
    line-height: 12px;
    font-size: 10px;
    color: rgb(147,153,159);
  }
  .rating-text{
    font-size: 12px;
    line-height: 12px;
    color: rgb(7,17,27);
  }
  .icon-up-hands{
    display: inline-block;
    width: 10px;
    height: 10px;
    background: rgb(0,160,220);
    border-radius: 50%;
  }
  .icon-down-hands{
    display: inline-block;
    width: 10px;
    height: 10px;
    background: rgb(147,153,159);
    border-radius: 50%;
  }
 .no-ratings{
   padding: 16px 0;
   font-size: 12px;
   color: rgb(147,153,159);
 }

</style>
