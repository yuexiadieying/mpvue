<template >
<view class="container" style='background:#e6e6e6'>
 <view style='width:100%;'>
   <view style='padding:10px 0;background:#fff;width:100%;z-index:1;' class='tc'>
     <i-row >
       <i-col span="6" i-class="col-class">
         <view @click="getMineOrderList('')"  class=' mt10' :class="status == ''?'colff6':'col3'">

           <view class='mt5'> 全部 </view>
         </view>
       </i-col>
       <i-col span="6" i-class="col-class">
         <view @click="getMineOrderList('0')"  class='mt10' :class="status == '0'?'colff6':'col3'">
           <view class='mt5'> 待付款 </view>
         </view>
       </i-col>
       <i-col span="6" i-class="col-class" >
         <view @click="getMineOrderList('5')" class='mt10 ' :class="status == '5'?'colff6':'col3'">
           <view class='mt5'> 待使用 </view>
         </view>
       </i-col>
       <i-col span="6" i-class="col-class" >
         <view @click="getMineOrderList('6')"  class='mt10 ' :class="status == '6'?'colff6':'col3'">
           <view class='mt5'> 已完成 </view>
         </view>
       </i-col>
     </i-row>
   </view>
   <view class='mt10 bgf'  v-for='(item,index) in orderList'>
     <view class='bbl' style='height:40px;line-height:40px;'>
       <i-row >
         <i-col span="18" i-class="col-class">
           <view class='ml10 fs17 col3'>
             下单时间：{{item.createDate}}
           </view>
         </i-col>
         <i-col span="6" i-class="col-class" >
           <view class='tr mr10 colff6 fs16' v-if="status =='0'">待付款</view>
           <view class='tr mr10 colff6 fs16' v-if="status =='5'">待使用</view>
           <view class='tr mr10 colff6 fs16' v-if="status =='6'">已完成</view>
         </i-col>
       </i-row>
     </view>
     <view v-for='(item1,index1) in item.shopUserProjectRelationDTOS' @click='orderMess(item.orderId,item1.sysShopProjectId)' class='bbl'>
       <i-row >
         <i-col span="7" i-class="col-class">
           <view class='ml10 mt10 mb5'>
             <img :src='item1.projectUrl' style='width:85px;height:85px;'/>
           </view>
          </i-col>
         <i-col span="17" i-class="col-class" >
           <view class=' mr10 col3 fs18 mt10'>{{item1.sysShopProjectName}}</view>
           <view class=' mr10 col6 fs16 mt10'>数量：{{item1.serviceTime }}</view>
           <view class=' mr10 col6 fs16 mt10'>预约金：<text class='colff6 fs18'>{{item1.advancePayment}}</text></view>
         </i-col>
       </i-row>
     </view>
     <view style='height:40px;line-height:40px;'>
       <i-row >
         <i-col span="13" i-class="col-class">
           <view >
             <text class=' fs16 colff6 ml10'>预付金:￥{{item.totalOrderAdvancePayment}}</text>
             <text class=' fs14 col3 ml10'>共:{{item.shopUserProjectRelationDTOS.length}}个项目</text>
           </view>
         </i-col>
         <i-col span="11" i-class="col-class" >
           <view class='tr'>
             <text class='fs14 colff6 mr10' style='border:2rpx solid #ff6666;border-radius:20px;background:none;height:30px;line-height:30px;display:inline-block;padding:0px 8px;'  @click.stop='paySuccess(item.orderId)' v-if="item.status == '5'">查看兑换码</text>
             <text class='fs14 colff6 mr10' style='border:2rpx solid #ff6666;border-radius:20px;background:none;height:30px;line-height:30px;display:inline-block;padding:0px 18px;'   @click.stop='pay(item.orderId,item.totalOrderAdvancePayment,item.totalArriveShopPayment)' v-if="item.status == '0'">付款</text>
           </view>
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
      status:'',
      orderList:[]
    }
  },
  async mounted () {
    await Promise.all([
       this.getMineOrderList('')
    ])
  },

  methods: {
    // 查看兑换码
    paySuccess (orderId) {
      wx.navigateTo({
        url: '/pages/businessCircle/paySuccess?orderId=' + orderId,
      })
     },
    async getMineOrderList(status){
      if (wx.getStorageSync('smallRoutineUserLoginToken') === '') {
        util.checkResponseData('mine')
        return
       }
      wx.showLoading({
        title: '加载中',
      })
      this.status = status
      const res =  await api.getMineOrderList({status:this.status});
      if(res.result === '0x00001'){
        this.orderList = res.responseData
        if(typeof this.orderList==='string'){
          this.orderList = []
           wx.hideLoading()
          return
        }
        if(this.orderList.length>0){
          for(var i=0;i<this.orderList.length;i++){
            this.orderList[i].createDate = util.dataFormat(this.orderList[i].createDate,'yyyy-MM-dd hh:mm:ss')
            console.log(this.orderList[i].createDate)
          }
        }
        wx.hideLoading()
      }
    },
    // 订单详情
    orderMess (orderId,sysShopProjectId) {
      wx.navigateTo({
        url: '/pages/businessCircle/orderMess?orderId='+orderId+'&&sysShopProjectId='+sysShopProjectId,
      })
    },
    // 去付款
    pay (orderId, allAdvance, totalArriveShopPayment) {
      wx.navigateTo({
        url: '/pages/businessCircle/pay?orderId=' + orderId + '&&allAdvance=' + allAdvance + '&&allShopMoney=' + totalArriveShopPayment
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
  .mb5{margin-bottom:5px;}
  .cold0{color:#d0d0d0}
  .col3{color:#333}
  .col6{color:#666}
  .bbl{border-bottom:2rpx solid #d0d0d0;}
  .bgf{background:#fff}
</style>
