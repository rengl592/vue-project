<template>
  <div class="login-form">
    <div class="g-form">
      <div class="g-form-line">
        <span class="g-form-label">用户名：</span>
        <div class="g-form-input">
          <input type="text"
                 v-model="usernameModel" placeholder="请输入用户名">
        </div>
        <span class="g-form-error">{{ userErrors.errorText }}</span>
      </div>
      <div class="g-form-line">
        <span class="g-form-label">密码：</span>
        <div class="g-form-input">
          <input type="password"
                 v-model="passwordModel" placeholder="请输入密码">
        </div>
        <span class="g-form-error">{{ passwordErrors.errorText }}</span>
      </div>
      <div class="g-form-line">
        <div class="g-form-btn">
          <a class="button" @click="onLogin">登录</a>
        </div>
      </div>
      <p>{{ errorTexts }}</p>
    </div>
  </div>
</template>

<script>
  export default{
    data(){
      return{
        usernameModel:'',
        passwordModel:'',
        errorTexts:''
      }
    },
    computed:{
      userErrors(){
        let status,errorText
        if(!/@/g.test(this.usernameModel)){
          status  = false
          errorText = '没有@'
        }
        else{
          status = true
          errorText = ''
        }
        if(!this.usenameFlag){
          errorText = ''
          this.usenameFlag = true
        }
        return{
          status,
          errorText
        }
      },
      passwordErrors(){
        let status,errorText
        if(!/^\w{6,16}$/g.test(this.passwordModel)){
          status  = false
          errorText = '请输入6-16有效密码'
        }
        else{
          status = true
          errorText = ''
        }
        if(!this.passwordFlag){
          errorText = ''
          this.passwordFlag = true
        }
        return{
          status,
          errorText
        }
      }
    },
    methods:{
      onLogin(){
        if (!this.userErrors.status || !this.passwordErrors.status){
          this.errorTexts = '输入有误，请重新输出！'
        }
        else {
          this.errorTexts = '',
//          console.log(this.usernameModel,this.passwordModel)
          this.$http.get('api/login')
            .then((res)=>{
              this.$emit('has-log',res.data)
            },(error)=>{
              console.log(error)
            })
        }
      }
    }
  }
</script>

<style scoped>

</style>
