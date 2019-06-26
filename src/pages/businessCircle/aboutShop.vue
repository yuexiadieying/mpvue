<template >
<view class="container">
  <view style='width:100%;background:#e6e6e6'>
    <view class='bgf'>
      <swiper class="banner" indicator-dots="true" autoplay="true" interval="3000" duration="1000" style='width:100%;height:200px' >
        <swiper-item v-for="item of imgUrls" :key="item" justify-content='center'>
          <img :src="item" style='width:100%;'  mode="widthFix"/>
        </swiper-item>
      </swiper>
    </view>
    <view class='bgf' style='padding:10px 0 ;'>
      <view class='fs20 ml10 bgf'>{{shopInfo.name}}</view>
        <view style='padding:0 10px;' class='mt10 bgf'>
          <i-row >
            <i-col span="16" i-class="col-class">
               <view class='fs17 col6'>营业时间: {{shopInfo.openAndCloseDoorDate}}</view>
            </i-col>
            <i-col span="8" i-class="col-class" >
               <view class='fs17 col6 tr'>{{shopInfo.appointNumber}}人已预约</view>
            </i-col>
           </i-row>
        </view>
        <view class='mt10 bgf' style='padding:0 10px;'>
          <i-row >
            <i-col span="16" i-class="col-class">
              <view class='fs17 col6'>地址：{{shopInfo.address}}</view>
            </i-col>
            <i-col span="8" i-class="col-class" >
              <view class='fs15 col6 tr'>据您{{shopInfo.distance}}km</view>
            </i-col>
          </i-row>
        </view>
    </view>
    <view style='margin-bottom:50px;' class='mt10 bgf'>
      <view class='fs18 ml10 bbl' style='height:40px;line-height:40px;text-weight:600;'>服务项目</view>
      <view style='padding:10px 10px;border-bottom:2rpx solid #d0d0d0;' v-for='(item,index) in shopInfo.shopProjectInfoList' @click='projectDetails(item.id,item.projectName)'>
        <i-row >
          <i-col span="8" i-class="col-class">
             <img :src='item.projectUrl' style='width:100px;height:100px;'/>
          </i-col>
          <i-col span="16" i-class="col-class">
            <view class='fs16'>{{item.projectName}}</view>
            <view class='fs16 mt10' style='height:20px;line-height:20px;'>{{item.projectFeatures==null?'':item.projectFeatures}}</view>
            <view class='mt20'>
              <i-row>
                <i-col span="13" i-class="col-class">
                  <view><text class='colff6 fs17 mr10'>¥{{item.marketPrice}}</text><text style='text-decoration:line-through' class='fs16'>¥{{item.initialPrice}}</text></view>
                </i-col>
                <i-col span="11" i-class="col-class">
                  <view class='fs14' style='text-align:right'>{{item.appointNumber}}人已预约</view>
                </i-col>
              </i-row>
            </view>
          </i-col>
         </i-row>
       </view>
    </view>
    <view style='position:fixed;bottom:0px;width:100%;font-family: PingFang-SC-Medium;'>
      <i-row style='height:50px;line-height:50px;'>
        <i-col span="6" i-class="col-class"  >
          <view style='background: #FFBF4E;'class='colf fs16 tc'@click='calling()'> <img src='/static/images/pone.png' style='height:18px;width:18px;'class='mr5'/>电话咨询</view>
        </i-col>
        <i-col span="18" i-class="col-class" >
         <view class='bgff6 fs18 colf tc' @click='appointment'> 预约</view>
        </i-col>
      </i-row>
    </view>
  </view>
</view>
</template>

<script>
import api from '@/utils/api'
import util from '@/utils/util'
import wx from 'wx'

export default {
  data () {
    return {
      imgUrls:[],
      syshopId:'',
      startLatitude:'',
      startLongitude:'',
      phone:'',
      shopInfo:'',
      startTime:'',
      endTime:''
    }
  },
  async mounted () {
    if (this.$route.query.syshopId) {
      this.syshopId = this.$route.query.syshopId;
    }
    this.startLatitude = util.shopInfo.startLatitude;
    this.startLongitude = util.shopInfo.startLongitude;
    await Promise.all([
      this.getShopInfo()
    ])
  },

  methods: {
    async getShopInfo (){
      wx.showLoading({
        title: '加载中',
      })
      const res =  await api.getOneSmallRoutineShopInfo({sysShopId:this.syshopId,startLatitude:this.startLatitude,startLongitude:this.startLongitude});
      if(res.result === '0x00001'){
        this.shopInfo = res.responseData
        this.imgUrls[0] =  this.shopInfo.zhaoxiangShopUrl
        this.phone = this.shopInfo.phone
        this.startTime=this.shopInfo.openAndCloseDoorDate.substring(0,this.shopInfo.openAndCloseDoorDate.indexOf('--'))
        this.endTime=this.shopInfo.openAndCloseDoorDate.substring(this.shopInfo.openAndCloseDoorDate.indexOf('--')+2, this.shopInfo.openAndCloseDoorDate.length)
         wx.hideLoading()
      }
     },
     //预约
    appointment (){
      if (wx.getStorageSync('smallRoutineUserLoginToken')=='') {
        util.checkResponseData('aboutShop')
        return
      }
      if(this.shopInfo.shopProjectInfoList<=0){
        util.titTop('敬请期待哦~')
        return
      }
      wx.navigateTo({
       url: '/pages/businessCircle/appointment?id=' + this.shopInfo.id + '&&startTime=' + this.startTime + '&&endTime=' + this.endTime,
      })
    },
    // 跳转到项目详情页
    projectDetails (id,name) {
      wx.navigateTo({
       url: '/pages/businessCircle/projectDetails?id=' + id + '&&name=' + name
      })
    },
    // 咨询
    calling:function () {
      wx.makePhoneCall({
        phoneNumber: this.phone,
        success:function(){
        }
      })
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

<style scoped>
 .tc{text-align:center}
 .tr{text-align:right}
 .fs12{font-size:12px;}
 .fs13{font-size:13px;}
 .fs14{font-size:14px;}
 .fs15{font-size:15px;}
 .fs16{font-size:16px;}
 .fs17{font-size:17px;}
 .fs18{font-size:18px;}
 .fs19{font-size:19px;}
 .fs20{font-size:20px;}
 .colff6{color:#ff6666}
 .mt5{margin-top:5px;}
 .mt10{margin-top:10px;}
 .mt20{margin-top:20px;}
 .mr10{margin-right:10px;}
 .ml10{margin-left:10px;}
 .cold0{color:#d0d0d0}
 .col3{color:#333}
 .col6{color:#666}
 .bbl{border-bottom:2rpx solid #d0d0d0;}
 .bgff6{background:#ff6666}
 .bgf{background:#fff}
 .colf{color:#fff}

</style>
