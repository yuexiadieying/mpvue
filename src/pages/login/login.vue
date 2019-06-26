<template >
<view class="container" delegate-handle="dashboard" >
  <div class="list" style='width:100%;'>
      <a class="bbl" style='padding:10px 10px;'>
          <h1 style="margin-top: 94px;color: #333333;font-size:34px;">您好，</h1>
          <span style="margin-top:18px;font-size:17px;color: #999999;display: inline-block">欢迎来到唯美帮</span>
      </a>
      <view class="bbl pdlr" >
          <input type="text" placeholder="请输入您的手机号" style='height: 50px;line-height:50px;color: #666;font-size:14px;' v-model="param.userPhone">
      </view>
      <view  class="bbl pdlr" >
          <input type="text" placeholder="请输入验证码" style='height: 50px;line-height:50px;color: #666;font-size:14px;'  v-model="param.validateCode">
      </view>
      <button class="button button-small" style=" margin-right:0.4rem;border: solid 1px;border-radius: 15px;;color: #999999; background: transparent;float: right;margin-top: -43px;z-index: 2;font-size:14px" v-if="param.validateCodeButtonStatus" @click="getValidateCode()">
          获取验证码
      </button>
      <button class="button button-small" style="margin-right:0.4rem;border: solid 1px;border-radius: 20px;  background: transparent;float: right;margin-top: -43px;z-index: 2;font-size:14px" v-if="!param.validateCodeButtonStatus">
          请{{param.timeCount}}秒后重新获取验证码
      </button>
      <button  open-type="getUserInfo" style="margin-top:38px;float:right;margin-right:30px;width:110px;background:#dddddd;color:white;border-radius:25px;font-size: 18px;color: white;"  @click="userLogin()" v-if="param.validateCode==''" > 登录
      </button>
      <button open-type="getUserInfo" style="margin-top:38px;float:right;margin-right:30px;width:110px;background:#ff6666;color:white;border-radius:25px;font-size: 18px;color: white;" v-if="param.validateCode!=''"  @click="userLogin()">登录
      </button>
  </div>
</view>
</template>
<script>
   import api from '@/utils/api'
   import util from '@/utils/util';
   import wx from 'wx'
   export default {
      data () {
       return {
          param: {
              userPhone:'',
              validateCode:'',
              validateCodeButtonStatus:true,
              timeCount: 60,
              redirectUrl:'',
              openId:'',
              code:'',
              nikeName:'',
              chatHeadImage:''
          }
       }
     },
     async mounted () {
       wx.hideTabBar({})

       if (this.$route.query.redirectUrl ) {
           this.param.redirectUrl = this.$route.query.redirectUrl;
       }
       wx.hideLoading()
       var that = this
       wx.login({
         success:function(res){
          that.param.code = res.code
          that.getOpenId()
         }
        })
       await Promise.all([
       ])
     },

     methods: {
       //h获取openId
       async getOpenId () {
          const res =  await api.getOpenId({code:this.param.code}); //openId获取
          this.param.openId = res.openid
       },
       //点击获取验证码
       async getValidateCode (){
         this.param.validateCodeButtonStatus = false;
         this.param.timeCount = 60;
         var that = this
         //每隔一秒执行
         var timer = setInterval(function(){
           that.param.timeCount--;
           if(that.param.timeCount<0){
               clearInterval(timer);
               that.param.validateCodeButtonStatus = true;
           }
         },1000);
         const res = await api.GetUserValidateCode({mobile:this.param.userPhone});
         if(res.result ==='0x00001'){
         }else{
           util.titTop('验证码获取失败，请重新获取')
           clearInterval(timer);
           this.param.validateCodeButtonStatus = true;
         }
       },
       async userLogin (){
           if(this.param.validateCode=='')
           {
               util.titTop('请输入验证码')
           }
           else
           {
               var that = this
               wx.getUserInfo({
                success:function(res) {
                  that.nickName = res.userInfo.nickName
                  that.chatHeadImage = res.userInfo.avatarUrl
                  that.loginTrue()

                },
                fail:function(res){
                   that.loginTrue()
                }
               })

           }
       },
      async loginTrue (){
         var loginRequestDTO ={
           userPhone:this.param.userPhone,
           code:this.param.validateCode,
           openId:this.param.openId,
           nickName: this.nickName,
           chatHeadImage:this.chatHeadImage
         }
         const res = await api.smallRoutineLogin(loginRequestDTO);
            if(res.result==='0x00002')
            {
               util.titTop(res.errorInfo)
            }else if(res.result==='0x00015'){
               util.titTop(res.errorInfo)
            }
            else if(res.result==='0x00010'){
               util.titTop('验证码输入有误')
            }
            else if(res.result==='0x11111'){
              util.titTop('账号已锁定，请联系客服')
            }
            else
            {
                if(res.responseData.smallRoutineUserLoginToken!='0x00006')
                {
                   wx.removeStorageSync('smallRoutineUserLoginToken')
                   wx.removeStorageSync('openId')
                   wx.setStorageSync('smallRoutineUserLoginToken', res.responseData.smallRoutineUserLoginToken);
                   wx.setStorageSync('openId', this.param.openId);
                   this.param.userPhone = ''
                   this.param.validateCode = ''
                }

                if(this.param.redirectUrl=='')
                {
                  wx.navigateTo({
                       url: '/pages/businessCircle/home'
                   })
                }
                else
                {
                   if(this.param.redirectUrl === 'mine'){
                       wx.switchTab({
                           url: '/pages/businessCircle/' + this.param.redirectUrl
                        })
                   }else{
                       wx.navigateTo({
                          url: '/pages/businessCircle/' + this.param.redirectUrl
                       })
                   }
                }
            }
       }
     },

     // 原生的分享功能
     onShareAppMessage: function () {
       return {
         title: '唯美召香皮肤管理中心',
         desc: '医疗美容',
         path: '/pages/businessCircle/home'
       }
     }
   }
</script>
<style>
 .bbl{
   border-bottom:1px solid #ccc
 }
 .pdlr{
  padding:0 10px;
 }
</style>
