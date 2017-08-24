<template>
 <div class="star" :class="starType">
   <span v-for="itemClass in itemStar" class="star-item" :class="itemClass">

   </span>
 </div>
</template>

<script>
  const LENGTH = 5;
  const CLS_ON = 'on';
  const CLS_HALF = 'half';
  const CLS_OFF = 'off';

    export default{
      props:{
        size:{
          type:Number
        },
        score:{
          type:Number
        }
      },
      computed:{
          starType(){
            return 'star-'+this.size
          },
        itemStar(){
          let result = [];
          let score = Math.floor(this.score * 2) / 2;
          let hasDecimal = score % 1 !== 0;
          let integer = Math.floor(score);
          for (let i=0;i<integer;i++){
            result.push(CLS_ON);
          }
          if(hasDecimal){
              result.push(CLS_HALF)
          }
          while (result.length<LENGTH){
              result.push(CLS_OFF);
          }
          return result;
        }
      }
    }
</script>

<style scoped>
  .star-48 .star-item{
    display: inline-block;
    width: 20px;
    height: 20px;
    margin-left: 22px;
  }
  .star-item:first-of-type{
    margin-left: 0;
  }
  .star-48 .on{
    background: url("../../assets/star48_on@2x.png") no-repeat center/cover;
  }
  .star-48 .half{
    background: url("../../assets/star48_half@2x.png") no-repeat center/cover;
  }
  .star-48 .off{
    background: url("../../assets/star48_off@2x.png") no-repeat center/cover;
  }

  .star-36 .star-item{
    display: inline-block;
    width: 15px;
    height: 15px;
    margin-left: 3px;
  }
  .star-item:first-of-type{
    margin-left: 0;
  }
  .star-36 .on{
    background: url("../../assets/star36_on@2x.png") no-repeat center/cover;
  }
  .star-36 .half{
    background: url("../../assets/star36_half@2x.png") no-repeat center/cover;
  }
  .star-36 .off{
    background: url("../../assets/star36_off@2x.png") no-repeat center/cover;
  }

  .star-24 .star-item{
    display: inline-block;
    width: 10px;
    height: 10px;
    margin-left: 3px;
  }
  .star-item:first-of-type{
    margin-left: 0;
  }
  .star-24 .on{
    background: url("../../assets/star24_on@2x.png") no-repeat center/cover;
  }
  .star-24 .half{
    background: url("../../assets/star24_half@2x.png") no-repeat center/cover;
  }
  .star-24 .off{
    background: url("../../assets/star24_off@2x.png") no-repeat center/cover;
  }
</style>
