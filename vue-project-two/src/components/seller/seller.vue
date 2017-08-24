<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="seller-overview">
        <h1 class="seller-title">{{seller.name}}</h1>
        <div class="seller-desc">
          <star :size="36" :score="seller.score"></star>
          <span class="seller-text">({{seller.ratingCount}})</span>
          <span class="seller-text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="seller-remark">
          <li class="seller-block">
            <h2>起送价</h2>
            <div class="content-text">
              <span class="text-stress">{{seller.minPrice}}</span>
              <span class="text-yuan">元</span>
            </div>
          </li>
          <li class="seller-block">
            <h2>商家配送</h2>
            <div class="content-text">
              <span class="text-stress">{{seller.deliveryPrice}}</span>
              <span class="text-yuan">元</span>
            </div>
          </li>
          <li class="seller-block">
            <h2>平均配送时间</h2>
            <div class="content-text">
              <span class="text-stress">{{seller.deliveryTime}}</span>
              <span class="text-yuan">元</span>
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <span class="favorite-icon" :class="{'active':favorite}">❤</span>
          <span class="favorite-text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="seller-bulletin">
        <h1 class="seller-bulletin-title">公告与活动</h1>
        <div class="content-wrapper">
          <p class="content-text_">{{seller.bulletin}}</p>
        </div>
        <ul v-if="seller.supports" class="supportses">
          <li class="supportses-item" v-for="(item,index) in seller.supports">
            <span class="supportses-icon" :class="classMap[seller.supports[index].type]"></span>
            <span class="supportses-text">{{seller.supports[index].description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="seller-pics">
        <h1 class="seller-pics-title">商家实景</h1>
        <div class="pics-wrapper" ref="picsWrapper">
          <ul class="pics-list" ref="picsList">
            <li class="pics-item" v-for="pic in seller.pics">
              <img width="120" height="90" :src="pic"  >
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="seller-info">
        <h1 class="seller-info-title">商家信息</h1>
        <ul>
          <li class="info-item" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
  import star from '../star/star'
  import split from '../split/split'
  import BScroll from 'better-scroll'
  import {saveToLocal,loadFromToLocal} from '../../common/js/store'

  export default{
    components:{
      star,
      split
    },
    props:{
      seller:{
        type:Object
      }
    },
    data(){
      return{
        favorite:(() => {
          return loadFromToLocal(this.seller.id, 'favorite', false);
        })()
      }
    },
    computed:{
      favoriteText(){
          return this.favorite? '已收藏':'收藏';
      }
    },
    created(){
      this.classMap = ['decrease','discount','special','invoice','gunarantee'];
    },
    watch:{
      seller(){
        this.$nextTick(() => {
          this._initScroll();
          this._initPics();
        });
      }
    },
    mounted(){
      this.$nextTick(() => {
        this._initScroll();
        this._initPics();
      });
    },
    methods:{
      toggleFavorite(event){
        if(!event._constructed){
          return;
        }
        this.favorite = !this.favorite;
        saveToLocal(this.seller.id,'favorite'.this.favorite);
      },
      _initScroll(){
        if(!this.scroll){
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$refs.seller,{
              click:true
            });
          });
        }else {
          this.scroll.refresh();
        }
      },
      _initPics(){
        if(this.seller.pics){
          let picWidth = 120;
          let picMargin = 6;
          let Width = (picWidth + picMargin) * this.seller.pics.length - picMargin;
          this.$refs.picsList.style.width = Width + 'px';
          this.$nextTick(() => {
            if(!this.picScroll){
              this.picScroll = new BScroll(this.$refs.picsWrapper,{
                scrollX: true,
                eventPassthrough: 'vertical'
              })
            }else{
              this.picScroll.refresh();
            }
          })
        }
      }
    }
  }
</script>

<style scoped>
  .seller{
    position: absolute;
    top: 174px;
    left: 0;
    bottom: 0;
    width: 100%;
    overflow: hidden;
  }
  .seller-overview{
    padding: 18px;
  }
  .seller-title{
    margin-bottom: 8px;
    line-height: 14px;
    color: rgb(7,17,27);
    font-size: 17px;
  }
  .seller-desc{
    padding-bottom: 18px;
    font-size: 0;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .star{
    display: inline-block;
    margin-right: 8px;
    vertical-align: top;
  }
  .seller-text{
    display: inline-block;;
    margin-right: 12px;
    vertical-align: top;
    font-size: 10px;
    line-height: 18px;
    color: rgb(77,85,93);
  }
  .seller-remark{
    display: flex;
    padding-top: 18px;
  }
  .seller-block{
    flex: 1;
    text-align: center;
    border-right: 1px solid rgba(7,17,27,0.1);
  }
  .seller-block:last-of-type{
    border: none;
  }
  .seller-block h2{
    margin-bottom: 4px;
    font-size: 10px;
    line-height: 10px;
    color: rgb(147,153,159);
  }
  .content-text{
    line-height: 24px;
    font-size: 10px;
    color: rgb(7,17,27);
  }
  .text-stress{
    font-size: 24px;
  }
  .favorite{
    position: absolute;
    width: 50px;
    right: 10px;
    top: 18px;
    text-align: center;
  }
  .favorite-icon{
    display: block;
    margin-bottom: 4px;
    font-size: 24px;
    line-height: 24px;
    color: #d4d6d9;
  }
  .favorite-icon.active{
    color: red;
  }
  .favorite-text{
    line-height: 10px;
    font-size: 10px;
    color: rgb(47,85,93);
  }

  .seller-bulletin{
    padding: 18px;
  }
  .seller-bulletin-title{
    margin-bottom: 8px;
    font-size: 17px;
    line-height: 14px;
    color: rgb(7,17,27);
  }
  .content-wrapper{
    padding: 8px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .content-text_{
    line-height: 24px;
    font-size: 12px;
    color: rgb(240,20,20);
  }

  .supportses-item{
    padding: 16px 12px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .supportses-item:last-of-type{
    border-bottom:none;
  }
  .supportses-item:last-of-type{
    margin-bottom: 0;
  }
  .supportses-icon{
    display: inline-block;
    width: 16px;
    height: 16px;
    vertical-align: top;
    margin-right: 6px;
  }
  .supportses-icon.decrease{
    background: url("../../assets/decrease_1@2x.png") no-repeat center/cover;
  }
  .supportses-icon.discount{
    background: url("../../assets/discount_1@2x.png") no-repeat center/cover;
  }
  .supportses-icon.special{
    background: url("../../assets/special_1@2x.png") no-repeat center/cover;
  }
  .supportses-icon.invoice{
    background: url("../../assets/invoice_1@2x.png") no-repeat center/cover;
  }
  .supportses-icon.gunarantee{
    background: url("../../assets/guarantee_1@2x.png") no-repeat center/cover;
  }
  .supportses-text{
    line-height: 16px;
    font-size: 12px;
    color: rgb(7,17,27);
  }
  .seller-pics{
    padding: 18px;
  }
  .seller-pics-title{
    margin-bottom: 12px;
    font-size: 17px;
    line-height: 14px;
    color: rgb(7,17,27);
  }
  .pics-wrapper{
    witdh: 100%;
    overflow: hidden;
    white-space: nowrap;
  }
  .pics-list{
    font-size: 0;
  }
  .pics-item{
    display: inline-block;
    margin-right: 6px;
    width: 120px;
    height: 90px;
  }
  .pics-item:last-of-type{
    margin: 0;
  }
  .seller-info{
    padding: 18px 18px 0 18px;
    color: rgb(7,17,27);
  }
  .seller-info-title{
    padding-bottom: 12px;
    font-size: 17px;
    line-height: 14px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .info-item{
    padding: 16px 12px;
    font-size: 12px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .info-item:last-of-type{
    border: none;
  }
</style>
