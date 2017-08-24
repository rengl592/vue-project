<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper" >
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex===index}" @click="selectMenu(index,$event)">
          <span class="menu-item-text">
            <span v-show="item.type>0" class="menu-item-icon" :class="classMap[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="food-list-title">{{item.name}}</h1>
          <ul>
            <li @click="selectFood(food,$event)" v-for="food in item.foods" class="food-item">
              <div class="food-icon">
                <img width="57" height="57" :src="food.icon" alt="">
              </div>
              <div class="food-content">
                <h2 class="food-name">{{food.name}}</h2>
                <p class="food-desc">{{food.description}}</p>
                <div class="food-extra">
                  <span class="food-extra-count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}</span>
                </div>
                <div class="food-price">
                  <span class="food-price-now">¥{{food.price}}</span>
                  <span class="food-price-old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food" @cart-add="cartAdd"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shop-cart ref="shopcart" :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shop-cart>
    <food ref="food" :food="selectedFood" @cart-add="cartAdd"></food>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  import shopCart from '../shopCart/shopCart'
  import cartcontrol from '../cartcontrol/cartcontrol'
  import food from '../food/food'

  const ERR_OK = 0;

  export default{
    components:{
      shopCart,
      cartcontrol,
      food
    },
    props:{
      seller:{
        type:Object
      }
    },
    data(){
      return{
        goods:[],
        listHeight:[],
        scrollY:0,
        selectedFood:{}
      }
    },
    computed:{
        //判断对应的高度
        currentIndex(){
          for(let i=0;i<this.listHeight.length;i++){
            let height_1 = this.listHeight[i];
            let height_2 = this.listHeight[i+1];
            if(!height_2 || (this.scrollY >= height_1 &&  this.scrollY < height_2)){
              return i;
            }
          }
        },
      selectFoods(){
        let foods = [];
        this.goods.forEach((good) => {
           good.foods.forEach((food) => {
            if(food.count){
              foods.push(food);
            }
           });
        });
        return foods;
//        console.log(this.goods)
      }
    },
    created(){
      this.classMap = ['decrease','discount','special','invoice','gunarantee'];

       this.$http.get('/api/goods')
       .then((res) => {
         res = res.body;
         if(res.errno === ERR_OK){
           this.goods = res.data;
//           console.log(this.goods)
            //$nextTick接口 获取更新后的DOM
           this.$nextTick(() => {
             this._initScroll();
             this._calculateHeight();
           });
          }
      },(err) => {
//         console.log(err)
       })
    },
    methods:{
      selectFood(food,event){
        if(!event._constructed){
          return;
        }
        this.selectedFood = food;
        this.$refs.food.show();
      },

      //对应右边
      selectMenu(index,event){
        if(!event._constructed){
          return;
        }

        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
          let el = foodList[index];
          this.foodsScroll.scrollToElement(el,300)
      },
      //滚动函数
      _initScroll(){
        this.menuScroll = new BScroll(this.$refs.menuWrapper,{
          //初始化属性
          click:true
        });
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper,{
          click:true,
          probeType:3
        });
        //对应左边
        this.foodsScroll.on('scroll',(pos) => {
            this.scrollY = Math.abs(Math.round(pos.y));
        })
      },
      //循环个数并添加
      _calculateHeight(){
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let Height = 0;
        this.listHeight.push(Height);
        for (let i=0;i<foodList.length;i++){
          let item = foodList[i];
          Height += item.clientHeight;
          this.listHeight.push(Height);
        }
      },
      cartAdd(target){
//        console.log(target);
        // 体验优化，异步执行下落动画
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target);
        })
      }
    }
  }
</script>

<style scoped>
  .goods{
  display: flex;
  position: absolute;
  top:174px;
  bottom: 46px;
  width: 100%;
  overflow: hidden;
}
  .menu-wrapper{
    flex: 0 0 80px;
    width: 80px;
    background: #f3f5f7;
  }
  .menu-item{
    display: table;
    width: 56px;
    height: 54px;
    padding: 0 12px;
    line-height: 14px;
  }
  .current{
    position: relative;
    z-index: -1;
    margin-top: -1px;
    background: #fff;
    font-weight: bold;
  }
  .current .menu-item-text{
    border: none;
  }
  .menu-item-text{
    display: table-cell;
    width: 56px;
    vertical-align: middle;
    font-size: 12px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .menu-item-icon{
    display: inline-block;
    vertical-align: top;
    width: 14px;
    height:14px;
    margin-right: 2px;
  }
  .menu-item-icon.decrease{
    background: url("../../assets/decrease_1@2x.png") no-repeat center/cover;
  }
  .menu-item-icon.discount{
    background: url("../../assets/discount_1@2x.png") no-repeat center/cover;
  }
  .menu-item-icon.special{
    background: url("../../assets/special_1@2x.png") no-repeat center/cover;
  }
  .menu-item-icon.invoice{
    background: url("../../assets/invoice_1@2x.png") no-repeat center/cover;
  }
  .menu-item-icon.gunarantee{
    background: url("../../assets/guarantee_1@2x.png") no-repeat center/cover;
  }

  .food-list-title{
    padding-left: 14px;
    height: 26px;
    line-height: 26px;
    border-left: 2px solid #d9dde1;
    font-size: 12px;
    color: rgb(147,153,159);
    background: #f3f5f7;
  }
  .food-item{
    position: relative;
    display: flex;
    margin: 18px;
    padding-bottom: 18px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .food-item:last-of-type{
    border-bottom: none;
    margin-bottom: 0;
  }
  .food-icon{
    flex: 0 0 57px;
    margin-right: 10px;
  }
  .food-content{
    flex: 1;
  }
  .food-name{
    margin: 2px 0 8px;
    height: 14px;
    line-height: 14px;
    font-size: 14px;
    color: rgb(7,17,27);
  }
  .food-desc{
    margin-bottom: 8px;
    line-height: 12px;
    font-size: 10px;
    color: rgb(147,153,159);
  }
  .food-extra {
    line-height: 10px;
    font-size: 10px;
    color: rgb(147,153,159);
  }
  .food-extra-count{
    display: inline-block;
    margin-right: 12px;
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
    right: 0;
    bottom: 12px;
  }


  .foods-wrapper{
    flex: 1;
  }
</style>
