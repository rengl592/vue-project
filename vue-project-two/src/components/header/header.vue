<template>
  <div class="header">
      <div class="comtent-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar" alt="">
      </div>
      <div class="content">
        <div class="content-title">
          <span class="brand"></span>
          <span class="content-name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="supports">
          <span class="supports-icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="supports-text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个&nbsp;></span>
      </div>
    </div>
    <div class="bulletin-wrapper">
      <span class="bulletin-title"></span>
      <span class="bulletin-text">{{seller.bulletin}}</span>
      <span class="icon-arrow-right">></span>
    </div>
    <div class="background">
      <img width="100%" height="100%" :src="seller.avatar" alt="">
    </div>
    <transition name="fade">
      <div v-show="detailShow" class="detail">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="detail-name">{{seller.name}}</h1>
            <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="message-title">
              <div class="message-line"></div>
              <div class="message-text">优惠信息</div>
              <div class="message-line"></div>
            </div>
            <ul v-if="seller.supports" class="supportses">
              <li class="supportses-item" v-for="(item,index) in seller.supports">
                <span class="supportses-icon" :class="classMap[seller.supports[index].type]"></span>
                <span class="supportses-text">{{seller.supports[index].description}}</span>
              </li>
            </ul>
            <div class="message-title">
              <div class="message-line"></div>
              <div class="message-text">商家公告</div>
              <div class="message-line"></div>
            </div>
            <div class="bulletins-text">
              <p class="bulletins-content">{{seller.bulletin}}</p>
            </div>
          </div>
        </div>
        <div class="detail-close" @click="detailHide">
          <span>×</span>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
  import star from '../star/star'
  export default{
      components:{
        star
      },

    //接收父组件绑定的seller传过来的数据
    props:{
        seller:{
            type:Object
        }
    },
    data(){
      return{
        detailShow:false
      }
    },
    methods:{
      showDetail:function(){
        this.detailShow=true
      },
      detailHide(){
          this.detailShow=false
      }
    },
    created(){
      this.classMap = ['decrease','discount','special','invoice','gunarantee'];
    }
  }
</script>

<style scoped>
  .header{
    position: relative;
    color: #fff;
    background: rgba(7,17,27,0.5);
    overflow: hidden;
  }
  .comtent-wrapper{
    position: relative;
    padding: 24px 12px 18px 24px;
    font-size: 0;
  }
  .avatar{
    display: inline-block;
    vertical-align: top;
  }
  .avatar>img{
    border-radius: 3px;
  }
  .content
  {
    display: inline-block;
    font-size: 14px;
    margin-left: 16px;
  }
  .content-title{
    margin: 2px 0 8px 0;
  }
  .brand{
    display: inline-block;
    vertical-align: top;
    width: 30px;
    height: 18px;
    background: url("../../assets/brand@2x.png") no-repeat center/cover;
  }
  .content-name{
    margin-left: 6px;
    font-size: 16px;
    font-weight: bold;
    line-height: 18px;
  }
  .description{
    margin-bottom: 10px;
    line-height: 12px;
    font-size: 12px;
  }
  .supports-icon{
    display: inline-block;
    vertical-align: top;
    width: 14px;
    height:14px;
    margin-right: 4px;
  }
  .supports-icon.decrease{
    background: url("../../assets/decrease_1@2x.png") no-repeat center/cover;
  }
  .supports-icon.discount{
    background: url("../../assets/discount_1@2x.png") no-repeat center/cover;
  }
  .supports-icon.special{
    background: url("../../assets/special_1@2x.png") no-repeat center/cover;
  }
  .supports-icon.invoice{
    background: url("../../assets/invoice_1@2x.png") no-repeat center/cover;
  }
  .supports-icon.gunarantee{
    background: url("../../assets/guarantee_1@2x.png") no-repeat center/cover;
  }
  .supports-text{
    font-size: 12px;
  }
  .support-count{
    position: absolute;
    right: 12px;
    bottom: 18px;
    padding: 0 8px;
    height: 24px;
    line-height: 24px;
    border-radius: 14px;
    background: rgba(0,0,0,0.2);
    text-align: center;
  }
  .count
  {
    font-size: 10px;
  }
  .bulletin-wrapper{
    position: relative;
    height: 28px;
    line-height: 28px;
    padding: 0 22px 0 12px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    background: rgba(7,17,27,0.2);
  }
  .bulletin-title{
    display: inline-block;
    width: 22px;
    height: 12px;
    background: url("../../assets/bulletin@2x.png") no-repeat center/cover;
  }
  .bulletin-title{
    margin: 0 3px;
    font-size: 10px;
  }
  .icon-arrow-right{
    position: absolute;
    right: 10px;
    font-size: 10px;
  }
  .background{
    position: absolute;
    top:0;
    left:0;
    width: 100%;
    height:100%;
    z-index: -1;
    filter: blur(10px);
  }
  .detail{
    position: fixed;
    z-index: 50;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    overflow: auto;
    background: rgba(7,17,27,0.8);
    /*-webkit-backdrop-filter: blur(10px);*/
  }
  .fade-enter,.fade-leave-to{
    opacity:0;
  }
  .fade-enter-active{
    transition:all 0.5s ease;
  }
  .fade-leave-active{
    transition:all 0.3s ease;
  }
  .fade-enter-to,.fade-leave{
    opacity:1;
  }
  .detail-wrapper{
    width: 100%;
    min-height: 100%;
  }
  .clearfix{
    display: inline-block;
  }
  clearfix:after{
    display: block;
    content: '.';
    height: 0;
    line-height: 0;
    clear: both;
    visibility: hidden;
  }
  .detail-main{
    margin-top: 64px;
    padding-bottom: 64px;
  }
  .detail-close{
    position: relative;
    width: 32px;
    height: 32px;
    margin: -64px auto 0;
    clear: both;
    font-size: 32px;
  }
  .detail-name{
    font-size: 16px;
    text-align: center;
    line-height: 16px;
    font-weight: bold;
  }
  .star-wrapper{
    margin-top: 18px;
    padding: 2px 0;
    text-align: center;
  }
  .message-title{
    display: flex;
    width: 80%;
    margin: 28px auto 24px;
  }
  .message-line{
    flex: 1;
    position: relative;
    top: -6px;
    border-bottom: 1px solid rgba(255,255,255,0.2);
  }
  .message-text{
    padding: 0 12px;
    font-weight: bold;
  }
  .supportses{
    width: 80%;
    margin: 0 auto;
  }
  .supportses-item{
    padding: 0 12px;
    margin-bottom: 12px;
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
  .bulletins-text{
    width: 80%;
    margin: 0 auto;
  }
  .bulletins-content{
    padding: 0 12px;
    font-size: 12px;
    line-height: 24px;
  }

</style>
