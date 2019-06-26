<template >
<view class="container">
 <view style='width:100%;'>
   <view style='padding:10px 5px;border-bottom:2rpx solid #d0d0d0;' v-for='(item,index) in shopList'>
     <i-row @click='projectDetails(item.id,item.phone,item.projectDetails,item.openAndCloseDoorDate)'>
       <i-col span="8" i-class="col-class">
         <img :src='item.zhaoxiangShopUrl' style='width:100px;height:100px;border-radius:50%;'/>
       </i-col>
       <i-col span="14" i-class="col-class">
         <view class='fs10 mt20'>{{item.name}}</view>
         <view class='cold0 fs17 mt10'>{{item.address}}</view>
       </i-col>
       <i-col span="2" i-class="col-class">
         <view style='margin-top:40px;'> <i-icon type="enter" size='22' /></view>
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
       shopList:[]
    }
  },
  async mounted () {
    this.shopList = util.shopInfo.shopList
    await Promise.all([
    ])
  },
  methods: {
    projectDetails (syshopId,phone,openAndCloseDoorDate){
      wx.navigateTo({
       url: '/pages/businessCircle/projectDetails?syshopId=' + syshopId +'&&phone=' + phone + '&&openAndCloseDoorDate=' + openAndCloseDoorDate
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
  .cold0{color:#d0d0d0}
  .col3{color:#333}
  .col6{color:#666}
  .bbl{border-bottom:2rpx solid #d0d0d0;}
</style>
