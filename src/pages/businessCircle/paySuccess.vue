<template >
<view class="container">
  <view style='width:100%'>
     <view style='height:100px;line-height:100px;text-align:bottom' class='tc' v-if="orderId!=''">兑换码:{{orderId}}</view>
     <view style='height:100px;line-height:100px;text-align:bottom' class='tc'>支付成功</view>
</view>
    <button style='background:#ff6666;width:200px;border-radius:20px;color:#fff;margin:40px auto;text-align:center;' @click='home'>回首页</button>
  </view>
</view>
</template>

<script>
import api from '@/utils/api'
import wx from 'wx'

export default {
  data () {
    return {
       orderId:''
    }
  },
  async mounted () {
    if (this.$route.query.orderId) {
      this.orderId = this.$route.query.orderId;
    }
  },

  methods: {
    //返回首页
    home (){
       wx.switchTab({
        url: '/pages/businessCircle/home',
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
