<template>
  <div id="app">
    <!--把seller数据赋值给seller传给子组件-->
    <v-header :seller="seller"></v-header>
    <div class="tab">
      <div class="tab-item">
        <keep-alive>
          <router-link :to="{path:'/goods'}">商品</router-link>
        </keep-alive>
      </div>
      <div class="tab-item">
        <keep-alive>
          <router-link :to="{path:'/ratings'}">评论</router-link>
        </keep-alive>
      </div>
      <div class="tab-item">
        <keep-alive>
          <router-link :to="{path:'/seller'}">商家</router-link>
        </keep-alive>
      </div>
    </div>
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script>
  import header from './components/header/header'
//  import {urlParse} from 'common/js/util'

  const ERR_OK = 0;
  export default{
    components:{
      'v-header':header
    },
    data() {
      return{
        seller:{
/**          id:(() => {
//            let queryParam = urlParse();
//            console.log(queryParam);
//            return queryParam.id;
         })()
*/
  }
      }
    },
    created(){
      this.$http.get('/api/seller?id='+ this.seller.id)
        .then((res) => {
          res = res.body;
          if (res.errno === ERR_OK){
            this.seller=res.data;
//            console.log(this.seller.deliveryPrice)
          }
      },(err) => {
//          console.log(err)
      })
    }
  }

</script>

<style>
  .tab{
    display: flex;
    width: 100%;
    height: 40px;
    line-height: 40px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
  }
  .tab .tab-item{
    flex: 1;
    text-align: center;
  }
  .tab .tab-item a{
    display: block;
    color: rgb(77,85,93);
  }
  .tab .tab-item a.active{
    color: rgb(240,20,20);
  }
</style>
