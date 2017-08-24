<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart">
        <span>-</span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add" @click.stop.prevent="addCart">+</div>
  </div>
</template>

<script>
  import Vue from 'vue'

  export default{
    props:{
      food:{
        type:Object
      }
    },
    created(){
//    console.log(this.food);
    },
    methods:{
      addCart(event){
        if(!event._constructed){
          return;
        }
        if(!this.food.count){
          Vue.set(this.food,'count',1)
        }else {
          this.food.count++;
        }
        //小球
        this.$emit('cart-add',event.target);
//        console.log(event.target);
      },
      decreaseCart(event){
        if(!event._constructed){
          return;
        }
        if(this.food.count){
          this.food.count--;
        }
      }
    }
  }
</script>

<style scoped>
  .cart-decrease{
    display: inline-block;
    border-radius: 50%;
    width: 16px;
    height: 16px;
    font-size: 24px;
    text-align: center;
    line-height: 12px;
    color: rgb(10,160,220);
    border: 2px solid rgb(10,160,220);
    border-radius: 50%;
  }
  .move-enter,.move-leave-to{
    opacity: 0;
    transform: translateX(24px) rotateZ(360deg);
  }
  .move-enter-active,.move-leave-active{
    transition: all 0.5s linear;
  }
  .move-enter-to,.move-leave{
    opacity: 1;
    transform: translateX(0) rotateZ(0);
  }
  .cart-count{
    display: inline-block;
    vertical-align: top;
    width: 12px;
    padding: 4px;
    text-align: center;
    font-size: 10px;
    color: rgb(147,153,159);
  }
  .cart-add{
    display: inline-block;
    width: 20px;
    height: 20px;
    font-size: 24px;
    text-align: center;
    line-height: 16px;
    color: #fff;
    background: rgb(10,160,220);
    border-radius: 50%;
  }
</style>
