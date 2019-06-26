<template >
<view class="container">

<view style='width:100%;'>
     <view style=''>
       <view style='padding:10px 5px;border-bottom:2rpx solid #d0d0d0;'>
         <i-row @click='aboutShop()'>
            <i-col span="8" i-class="col-class">
              <img :src='shopUserProjectRelationDTOS.projectUrl  ' style='width:80px;height:80px;background:pink;border-radius:50%;'/>
            </i-col>
            <i-col span="16" i-class="col-class">
                <view class='fs20 mt10'>{{shopUserProjectRelationDTOS.sysShopProjectName}}</view>
                <view class='colff6 fs23 mt20'>预付金:{{shopUserProjectRelationDTOS.advancePayment}}</view>
            </i-col>
          </i-row>
      </view>
      <view style='height:60px;line-height:60px;' v-if="shopUserOrder.status=='5'">

         <i-button  type="error" class='fs18' @click='paySuccess(shopUserOrder.orderId)' >查看兑换码</i-button>
      </view>
    </view>
    <view style='padding:10px;' class='mr10'>
      <view class='fs20'>订单信息</view>
      <view style='height:30px;line-height:30px;width:100%;margin-top:10px;background:#fff' class='fs16'>
          <i-row >
             <i-col span="10" i-class="col-class">
               <text style='font-size: 16px; color: #333333;margin-left:10px;'>项目金额:</text>
             </i-col>
             <i-col span="14" i-class="col-class">
                <view class='fs16 col3 tr'>  ¥{{shopUserProjectRelationDTOS.sysShopProjectPurchasePrice}}</view>
              </i-col>
          </i-row>
       </view>
       <view style='height:30px;line-height:30px;width:100%;margin-top:10px;background:#fff' class='fs16'>
         <i-row >
            <i-col span="10" i-class="col-class">
              <text style='font-size: 16px; color: #333333;margin-left:10px;'>订单合计:</text>
            </i-col>
            <i-col span="14" i-class="col-class">
               <view class='fs16 col3 tr'>  ¥{{orderMess.totalOrderPrice}}</view>
             </i-col>
         </i-row>
       </view>
       <view style='height:30px;line-height:30px;width:100%;margin-top:10px;background:#fff' class='fs16'>
          <i-row >
             <i-col span="10" i-class="col-class">
               <text style='font-size: 16px; color: #333333;margin-left:10px;'>商品数量:</text>
             </i-col>
             <i-col span="14" i-class="col-class">
                <view class='fs16 col3 tr'>{{shopUserProjectRelationDTOS.serviceTime}}</view>
              </i-col>
          </i-row>
        </view>
         <view style='height:30px;line-height:30px;width:100%;margin-top:10px;background:#fff' class='fs16'>
            <i-row >
               <i-col span="10" i-class="col-class">
                 <text style='font-size: 16px; color: #333333;margin-left:10px;'>到店再付:</text>
               </i-col>
               <i-col span="14" i-class="col-class">
                  <view class='fs16 col3 tr'>  ¥{{shopUserProjectRelationDTOS.arriveShopPayment}}</view>
                </i-col>
            </i-row>
          </view>
          <view style='height:30px;line-height:30px;width:100%;margin-top:10px;background:#fff' class='fs16'>
            <i-row >
               <i-col span="10" i-class="col-class">
                 <text style='font-size: 16px; color: #333333;margin-left:10px;'>预约时间:</text>
               </i-col>
               <i-col span="14" i-class="col-class">
                  <view class='fs16 col3 tr'>{{orderMess.appointStartTime}}</view>
                </i-col>
            </i-row>
          </view>
         <view style='height:30px;line-height:30px;width:100%;margin-top:10px;background:#fff' class='fs16'>
            <i-row >
               <i-col span="10" i-class="col-class">
                 <text style='font-size: 16px; color: #333333;margin-left:10px;'>订单编号:</text>
               </i-col>
               <i-col span="14" i-class="col-class">
                  <view class='fs16 col3 tr'>{{shopUserOrder.orderId}}</view>
                </i-col>
            </i-row>
          </view>
          <view style='height:30px;line-height:30px;width:100%;margin-top:10px;background:#fff' class='fs16'>
            <i-row >
               <i-col span="10" i-class="col-class">
                 <text style='font-size: 16px; color: #333333;margin-left:10px;'>手机号:</text>
               </i-col>
               <i-col span="14" i-class="col-class">
                  <view class='fs16 col3 tr'>{{shopUserOrder.mobile}}</view>
                </i-col>
            </i-row>
          </view>
          <view style='height:30px;line-height:30px;width:100%;margin-top:10px;background:#fff' class='fs16'>
            <i-row >
               <i-col span="10" i-class="col-class">
                 <text style='font-size: 16px; color: #333333;margin-left:10px;'>店铺名称:</text>
               </i-col>
               <i-col span="14" i-class="col-class">
                  <view class='fs16 col3 tr'>{{orderMess.name}}</view>
                </i-col>
            </i-row>
          </view>
          <view style='height:30px;line-height:30px;width:100%;margin-top:10px;background:#fff' class='fs16'>
            <i-row >
               <i-col span="10" i-class="col-class">
                 <text style='font-size: 16px; color: #333333;margin-left:10px;'>咨询电话:</text>
               </i-col>
               <i-col span="14" i-class="col-class">
                  <view class='fs16 col3 tr' @click='calling'>{{orderMess.phone}}</view>
                </i-col>
            </i-row>
          </view>
          <view style='height:30px;line-height:30px;width:100%;margin-top:10px;background:#fff' class='fs16'>
            <i-row >
               <i-col span="10" i-class="col-class">
                 <text style='font-size: 16px; color: #333333;margin-left:10px;'>地址:</text>
               </i-col>
               <i-col span="14" i-class="col-class">
                  <view class='fs16 col3 tr'>{{orderMess.address}}</view>
                </i-col>
            </i-row>
          </view>
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
       orderId:'',
       sysShopProjectId:'',
       orderMess:'',
       shopUserOrder:'',
       shopUserProjectRelationDTOS:""
    }
  },
  async mounted () {
   if (this.$route.query.orderId&& this.$route.query.sysShopProjectId) {
     this.orderId = this.$route.query.orderId;
     this.sysShopProjectId = this.$route.query.sysShopProjectId;
   }
    await Promise.all([
      this.getMineOrderMess()
    ])
  },

  methods: {
    async getMineOrderMess(){
      if (wx.getStorageSync('smallRoutineUserLoginToken')=='') {
          util.checkResponseData('orderMess')
          return
      }
      wx.showLoading({
        title: '加载中',
      })
      const res =  await api.getOrderInfo({orderId:this.orderId,sysShopProjectId:this.sysShopProjectId});
      if(res.result === '0x00001'){
         this.orderMess = res.responseData
         this.shopUserOrder = res.responseData.shopUserOrder
         this.shopUserProjectRelationDTOS = res.responseData.shopUserOrder.shopUserProjectRelationDTOS[0]
         this.orderMess.appointStartTime= util.dataFormat(this.orderMess.appointStartTime, 'yyyy-MM-dd hh:mm:ss')
         wx.hideLoading()
      }
    },
    //去付款
    pay (){
      wx.navigateTo({
       url: '/pages/businessCircle/pay',
      })
    },
   //查看兑换码
    paySuccess (orderId){
      wx.navigateTo({
       url: '/pages/businessCircle/paySuccess?orderId='+orderId,
      })
    },
    calling (){
      wx.makePhoneCall({
        phoneNumber: this.orderMess.address,
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
.i-btn{font-size:16px;}
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
 .mt23{margin-top:23px;}
 .mr10{margin-right:10px;}
 .ml10{margin-left:10px;}
 .cold0{color:#d0d0d0}
 .col3{color:#333}
 .col6{color:#666}
 .bbl{border-bottom:2rpx solid #d0d0d0;}
 .bgf{background:#fff}

</style>
