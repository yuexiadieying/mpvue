<template >
<view class="container" style='background:#e6e6e6'>
  <view style='height:40px;line-height:40px;width:100%;background:#fff'>
     <i-row >
        <i-col span="10" i-class="col-class">
          <text style='font-size: 16px; color: #333333;margin-left:10px;'>召香订单号:</text><text style='font-size: 16px;color:#ff6666;'>*</text>
        </i-col>
        <i-col span="14" i-class="col-class">
           <view class='fs16 col3 tr mr10'>{{orderId}}</view>
         </i-col>
     </i-row>
  </view>
  <view style='height:40px;line-height:40px;width:100%;background:#fff'>
     <i-row >
        <i-col span="10" i-class="col-class">
          <text style='font-size: 16px; color: #333333;margin-left:10px;'>预约金合计:</text><text style='font-size: 16px;color:#ff6666;'>*</text>
        </i-col>
        <i-col span="14" i-class="col-class">
           <view class='fs16 col3 tr mr10'>￥{{allAdvance}}</view>
         </i-col>
     </i-row>
  </view>
  <view style='height:40px;line-height:40px;width:100%;background:#fff'>
       <i-row >
          <i-col span="10" i-class="col-class">
            <text style='font-size: 16px; color: #333333;margin-left:10px;'>到店再付:</text><text style='font-size: 16px;color:#ff6666;'>*</text>
          </i-col>
          <i-col span="14" i-class="col-class">
             <view class='fs16 col3 tr mr10'>￥{{allShopMoney}}</view>
           </i-col>
       </i-row>
    </view>
   <view style='height:50px;line-height:50px;width:100%;background:#fff;margin-top:10px;'>
       <i-row >
          <i-col span="20" i-class="col-class">
            <text style='font-size: 16px; color: #333333;margin-left:10px;'>微信支付</text><text style='font-size: 16px;color:#ff6666;'></text>
          </i-col>
          <i-col span="4" i-class="col-class">
            <view class='tr'>
               <i-radio  checked="checked" > </i-radio>
            </view>
           </i-col>
       </i-row>
    </view>
  <view style='height:50px;line-height:50px;background:#ff6666;color:#fff;position:fixed;bottom:0;width:100%;z-index:1' @click='payOrder' class='fs18 tc'>
     确认支付
  </view>
</view>
</template>

<script>
import api from '@/utils/api'
import util from '@/utils/util'
import wx from 'wx';
export default {
  data () {
    return {
       orderId:'',
       allAdvance:'',
       code:'',
       openid:'',
       flag:true,
       userShopAppointId:'',
       allShopMoney:''
    }
  },
  async mounted () {
     if (this.$route.query.orderId && this.$route.query.allAdvance&& this.$route.query.allShopMoney) {
       this.orderId = this.$route.query.orderId;
       this.allAdvance = this.$route.query.allAdvance;
       this.userShopAppointId = this.$route.query.userShopAppointId;
       this.allShopMoney = this.$route.query.allShopMoney;
     }
    await Promise.all([
      this.getGoodsNew()
    ])
  },

  methods: {
     async payOrder (){
        if(!this.flag)return
        this.flag = false
        var that = this
        this.openid = wx.getStorageSync('openId')
        var wxPayRequest = {
          orderId:this.orderId,
          orderSource:'1',
          orderAdvancePayment: this.allAdvance,
          openid:this.openid
         }
         const res1 =  await api.prepayIdNew(wxPayRequest);
         if(res1.success === true){
            wx.requestPayment({  //支付
             'appId':res1.data.appid,
             'timeStamp': res1.data.timeStamp,
             'nonceStr': res1.data.nonceStr,
             'package': res1.data.package,
             'signType': 'MD5',
             'paySign': res1.data.paySign,
             'success': function (res) {
                that.changeOrderStatus()
                that.sendMess()
                that.flag = true
                wx.redirectTo({
                  url: '/pages/businessCircle/paySuccess?orderId=' + that.orderId,
                })
             },
             'fail': function (res) {
               that.flag = true
               util.titTop('支付异常')

             }
           })
         }else{
            util.showErrorToast('订单异常');
         }
     },
      async changeOrderStatus(){
         var shopUserOrderDTO ={
           orderId: this.orderId,
           status: '5',
           orderSource:'1',
           userShopAppointId:this.userShopAppointId
         }
        const res =  await api.updateOrderInfoNew(shopUserOrderDTO);
      },
       //发送信息
        async sendMess () {
           const res =  await api.sendMobileMessageNew({orderId:this.orderId});
              if (res.result === '0x00001'){

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
  .cold0{color:#d0d0d0}
  .col3{color:#333}
  .col6{color:#666}
  .bbl{border-bottom:2rpx solid #d0d0d0;}
</style>
