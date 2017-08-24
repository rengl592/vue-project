<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="ratings-overview">
        <div class="ratings-overview-left">
          <h1 class="ratings-score">{{seller.score}}</h1>
          <div class="ratings-title">综合评分</div>
          <div class="ratings-rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="ratings-overview-right">
          <div class="score-wrapper">
            <span class="wrapper-title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="wrapper-title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="wrapper-title">送达时间</span>
            <span class="delivery-time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="ratings" @ratingSelect="ratingSelect" @ratingToggle="ratingToggle"></ratingselect>
      <div class="ratings-wrapper">
        <ul>
          <li v-for="ratings in ratings" v-show="needShow(ratings.rateType, ratings.text)" class="ratings-item">
            <div class="ratings-avatar">
              <img width="28" height="28" :src="ratings.avatar" alt="">
            </div>
            <div class="ratings-content">
              <h1 class="ratings-name">{{ratings.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="ratings.score"></star>
                <span class="delivery" v-show="ratings.deliveryTime">
                  {{ratings.deliveryTime}}分钟送达
                </span>
              </div>
              <p class="ratings-text">{{ratings.text}}</p>
              <div class="recommend" v-show="ratings.recommend && ratings.recommend.length">
                <span class="icon-up-hands"></span>
                <span class="recommend-item" v-for="item in ratings.recommend">{{item}}</span>
              </div>
              <div class="ratings-time">
                {{ratings.rateTime | formaDate}}
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  import star from '../star/star'
  import split from '../split/split'
  import ratingselect from '../ratingselect/ratingselect'
  import {formaDate} from '../../common/js/date'

  const ALL = 2;
  const ERR_OK = 0;

  export default{
    components:{
      star,
      split,
      ratingselect
    },
    props:{
      seller:{
        type:Object
      }
    },
    filters: {
      formaDate(time) {
        let date = new Date(time);
        return formaDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    data(){
      return{
        showFlag:false,
        selectType:ALL,
        onlyContent:true,
        ratings:[],
        desc:{
          all:'全部',
          positive:'推荐',
          negative:'吐槽'
        }
      }
    },
    created(){
      this.$http.get('/api/ratings').then((res) => {
        res = res.body;
        if(res.errno===ERR_OK){
          this.ratings = res.data;
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$refs.ratings,{
              click:true
            });
          })
        }
      });
    },
    methods:{
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
  .ratings{
    position: absolute;
    top: 174px;
    left: 0;
    bottom: 0;
    width: 100%;
    overflow: hidden;
  }
  .ratings-overview{
    display: flex;
    padding: 18px 0;
  }
  .ratings-overview-left{
    flex: 0 0 137px;
    padding: 6px 0;
    width: 137px;
    border-right: 1px solid rgba(7,17,27,0.1);
    text-align: center;
  }
  .ratings-score{
    margin-bottom: 6px;
    line-height: 28px;
    font-size: 24px;
    color: rgb(255,153,0);
  }
  .ratings-title{
    margin-bottom: 8px;
    line-height: 12px;
    font-size: 12px;
    color: rgb(7,17,27);
  }
  .ratings-rank{
    line-height: 10px;
    font-size: 10px;
    color: rgb(147,153,159);
  }
  .ratings-overview-right{
    flex: 1;
    padding:6px 0 6px 24px;
  }
  .score-wrapper{
    margin-bottom: 8px;
    font-size: 0;
  }
  .wrapper-title{
    display: inline-block;
    vertical-align: top;
    font-size: 12px;
    line-height: 18px;
    color: rgb(7,17,27);
  }
  .ratings-overview-right .star{
    display: inline-block;
    margin: 0 12px;
    vertical-align: top;
  }
  .score{
    font-size: 12px;
    color: rgb(255,153,0);
    line-height: 18px;
  }
  .delivery-wrapper{
    font-size: 0;
  }
  .delivery-time{
    margin-left: 12px;
    font-size: 12px;
    line-height: 18px;
    color: rgb(147,153,159);
  }
  .ratings-wrapper{
    padding: 0 18px;
  }
  .ratings-item{
    display: flex;
    padding: 18px 0;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .ratings-avatar{
    flex: 0 0 28px;
    width: 28px;
    margin-right: 12px;
  }
  .ratings-avatar>img{
    border-radius: 50%;
  }
  .ratings-content{
    position: relative;
    flex: 1;
  }
  .ratings-name{
    margin-bottom: 4px;
    line-height: 12px;
    font-weight: bold;
    font-size: 10px;
    color: rgb(7, 17, 27);
  }
  .star-wrapper{
    margin-bottom: 6px;
    font-size: 0;
  }
  .ratings-wrapper .star{
    display: inline-block;
    margin-right: 16px;
    vertical-align: top;
  }
  .delivery{
    display: inline-block;
    vertical-align: top;
    font-size: 10px;
    line-height: 12px;
    color: rgb(147,153,159);
  }
  .ratings-text{
    margin-bottom: 8px;
    font-size: 12px;
    color: rgb(7,17,27);
    line-height: 18px;
  }
  .recommend{
    line-height: 16px;
    font-size: 9px;
  }
  .icon-up-hands{
    display: inline-block;
    margin-right: 8px;
    width: 10px;
    height: 10px;
    background: rgb(0,160,220);
    border-radius: 50%;
  }
  .recommend-item{
    display: inline-block;
    padding: 0 6px;
    margin-bottom: 3px;
    color: rgb(147, 153, 159);
    background: #fff;
    border: 1px solid rgba(7, 17, 27, 0.1);
    border-radius: 1px;
  }
  .ratings-time{
    position: absolute;
    top: 0;
    right: 0;
    line-height: 12px;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }

  /*苹果5----320*/
  @media only screen and (max-width:320px){
    .ratings-overview-left{
      flex: 0 0 120px;
      width: 120px;
    }
    .ratings-overview-right{
      padding:6px 0 6px 6px;
    }
  }
</style>
