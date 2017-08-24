<template>
  <div class="ratingselect">
    <div class="rating-type">
      <span @click="select(2,$event)" class="block positive" :class="{'positive-active':selectType===2}">{{desc.all}}
        <span class="rating-count">{{ratings.length}}</span>
      </span>
      <span @click="select(0,$event)" class="block positive" :class="{'positive-active':selectType===0}">{{desc.positive}}
        <span class="rating-count">{{positive.length}}</span>
      </span>
      <span @click="select(1,$event)" class="block negative" :class="{'negative-active':selectType===1}">{{desc.negative}}
        <span class="rating-count">{{negative.length}}</span>
      </span>
    </div>
    <div @click="toggleContent" class="rating-switch">
      <span class="icon-check" :class="{'check-text-active':onlyContent}">√</span>
      <span class="check-text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
  const POSITIVE = 0;
  const NEGATIVE = 1;
  const ALL = 2;

  export default{
    props:{
      ratings:{
        type:Array,
        default(){
          return[];
        }
      },
      selectType:{
        type:Number,
        default:ALL
      },
      onlyContent: {
        type: Boolean,
        default: false
      },
      desc:{
        type:Object,
        default(){
          return{
            all:'全部',
            positive:'满意',
            negative:'不满意',
          }
        }
      }
    },
    methods:{
      select(type,event){
        if(!event._constructed){
          return;
        }
//        this.selectType = type;
        this.$emit('ratingSelect',type);
      },
      toggleContent(){
        if(!event._constructed){
          return;
        }
//        this.onlyContent = !this.onlyContent
        this.$emit('ratingToggle',this.onlyContent);
      }
    },
    computed:{
      positive(){
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE;
        });
      },
      negative(){
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE;
        });
      }
    }
  }
</script>

<style scoped>
  .rating-type{
    padding: 18px 0;
    margin: 0 18px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .block{
    display: inline-block;
    padding: 8px 12px;
    margin-right: 8px;
    border-radius: 2px;
    color: rgb(77,85,93);
    font-size: 12px;
    line-height: 16px;
  }
  .positive{
    background: rgba(0,160,220,0.2);
  }
  .negative{
    background: rgba(77,85,93,0.2);
  }
  .positive-active{
    color: #fff;
    background: rgb(0,160,220);
  }
  .negative-active{
    color: #fff;
    background: rgb(77,85,93);
  }
  .rating-count{
    margin-left: 2px;
    font-size: 8px;
  }

  .rating-switch{
    padding: 12px 18px;
    line-height: 24px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
    color: rgb(147,153,159);
  }
  .icon-check{
    display: inline-block;
    width: 20px;
    height:20px;
    color: #fff;
    font-size: 12px;
    line-height: 20px;
    text-align: center;
    border-radius: 50%;
    background: rgb(147,153,159);
  }
  .check-text-active{
    background: #00c850;
  }
  .check-text{
    display: inline-block;
    font-size: 12px;
  }
</style>
