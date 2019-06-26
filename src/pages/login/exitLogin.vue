<template >
<view class="container" delegate-handle="dashboard" >
  <view class="list" style='width:100%;'>
     <view style='background:#6AB5F3;padding:10px 0; '>
       <i-row >
          <i-col span="6" i-class="col-class">
              <view><img :src='mineInfo.chatHeadImage' style='width:80px;height:80px;;border-radius:50%;margin:15px 0 0 10px;background:#e5e5e5;'/></view>
          </i-col>
          <i-col span="18" i-class="col-class" >
          <view class='fs10 ml10 ' style='margin-top:40px;color:#fff'>{{mineInfo.nickName==null?'':mineInfo.nickName}}</view>
          </i-col>
       </i-row>
       <button open-type="getUserInfo"  @click='login' style='opacity:0;position:absolute;top:25px;left:10px;width:200px;height:80px;border-radius:20%;'></button>
     </view>

      <view style='width:100%;position:fixed;bottom:0'><button style='width:100%;height:60px;line-height:60px;border-radius:5px;background:#6AB5F3;color:#fff'  @click='exitLogin'>退出</button></view>

  </view>
</view>
</template>
<script>
   import api from '@/utils/api'
   import util from '@/utils/util';
   import wx from 'wx'
   export default {
      data () {
       return {
          mineInfo:''
       }
     },
     async mounted () {
       await Promise.all([
         this.getNikeName()
       ])
     },

     methods: {
        async getNikeName (){
           const res =  await api.getMineInfo();
           if(res.result === '0x00001'){
             this.mineInfo = res.responseData
           }
        },
         exitLogin (){
           wx.removeStorageSync('smallRoutineUserLoginToken')
           wx.removeStorageSync('openId')
           wx.reLaunch({
            url: '/pages/businessCircle/home?'
           })
           util.titTop('退出成功')
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
