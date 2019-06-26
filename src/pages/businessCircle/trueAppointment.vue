<template >
<view class="container" style='background:#e6e6e6'>
  <view style='width:100%;' v-for='(item,index) in info.shopProjectInfoResponseList' class='mb10'>
   <view style='width:100%;padding:10px 10px;background:#fff;'  class='bbl' >
     <i-row >
       <i-col span="6" i-class="col-class"  >
         <img :src='item.projectUrl' style='width:60px;;height:60px;margin-left:10px;'/>
       </i-col>
       <i-col span="18" i-class="col-class" >
         <view style='font-size: 15px;color: #666666;margin-left:10px;margin-right:10px;'>{{item.projectName}} </view>
         <view style='margin-top:10px;padding-top:1px;margin-left:10px;'>
           <i-row>
             <i-col span="11" i-class="col-class" style='font-size: 18px;color: #FF6666;' >
                ¥{{item.marketPrice}}
             </i-col>
             <i-col span="12" i-class="col-class" >
               <view style='margin-left:30px;margin-top:10px;' >
                 <text style='width:18px;height:18px;background:#F3F3F3;display:inline-block;text-align:center;line-height:18px;'@click='number(-1,index)'>-</text>
                 <view style='display:inline-block;margin:5px;width:30px;height:18px;'>
                 <input  class="i-input-number-text" type="number" v-model="item.num" selection-start="-1" selection-end="-1" cursor="-1" style='background:#F3F3F3;display:inline-block;height:18px;line-height:18px;text-align:center' > </input>
                 </view>
                 <text style='width:18px;height:18px;line-height:18px;background:#F3F3F3;display:inline-block;text-align:center;' @click='number(1,index)'>+</text>
               </view>
             </i-col>
           </i-row>
         </view>
       </i-col>
     </i-row>
  </view>
  <view style='height:50px;line-height:50px;width:100%;background:#fff' class='bbl'>
    <i-row >
      <i-col span="10" i-class="col-class">
        <text style='font-size: 16px; color: #333333;margin-left:10px;'>预约金:</text><text style='font-size: 16px;color:#ff6666;' >*</text>
      </i-col>
      <i-col span="14" i-class="col-class">
        <view class='fs16 col3 tr mr10'>￥{{item.advancePayment}}</view>
      </i-col>
    </i-row>
  </view>
  <view style='height:50px;line-height:50px;width:100%;background:#fff' class='bbl'>
    <i-row >
      <i-col span="10" i-class="col-class">
        <text style='font-size: 16px; color: #333333;margin-left:10px;'>到店需付:</text><text style='font-size: 16px;color:#ff6666;'>*</text>
      </i-col>
      <i-col span="14" i-class="col-class">
        <view class='fs16 col3 tr mr10'>￥{{item.shopMoney}}</view>
      </i-col>
    </i-row>
  </view>
  </view>
  <view style='height:50px;line-height:50px;width:100%;margin-top:10px;background:#fff'>
    <i-row >
      <i-col span="10" i-class="col-class">
        <text style='font-size: 16px; color: #333333;margin-left:10px;'>预约时间:</text><text style='font-size: 16px;color:#ff6666;'>*</text>
      </i-col>
      <i-col span="14" i-class="col-class">
         <view class='fs16 col3 tr'> {{info.appointStartTime}}</view>
       </i-col>
    </i-row>
  </view>
  <view style='width:100%;background:#fff;margin-bottom:70px;'>
    <i-row >
      <i-col span="10" i-class="col-class">
        <view style='line-height:50px;height:50px'><text style='font-size: 16px; color: #333333;margin-left:10px;'>地址:</text><text style='font-size: 16px;color:#ff6666;'>*</text></view>
      </i-col>
      <i-col span="14" i-class="col-class">
         <view class='fs16 col3 tr mr10'> {{info.shopAddress}}</view>
      </i-col>
    </i-row>
  </view>

   <view style='position:fixed;top:0px;width:100%;z-index:2; background:rgba(0,0,0,0.3);height:100%' v-if='titleFlag' @click='hideTitle()'></view>
   <view  style='border-radius:12px;padding:10px;position:absolute;top:150px;left:50%;z-index:2;width:230px;margin-left:-110px;' class='bgf' v-if='titleFlag'>
     <view class='colff6 fs18 tc'>什么是预约金？</view>
     <view class='fs16 col3 mt10'>预约金是召香项目需要在预约项目时先支付的费用，到店后支付剩余费用即可。</view>
     <view class='fs16 col3 mt10'> 即，预约金+到店支付金额=项目价格</view>
     <view class='fs16 col3 mt10'>注：预约金过期未消费，可到该店铺进行退款。</view>
   </view>
   <view style='height:60px;position:fixed;bottom:0;width:100%;z-index:1;background:#fff' class='btl'>
     <i-row >
       <i-col span="16" i-class="col-class">
         <view class='fs18 col3 ml10'><text class='colff6 fs18'>预约金：￥{{allAdvance}}</text><i-icon type="prompt_fill" size='22' color='#ff6666' @click='showTitle()' /></view>
         <view class='fs16 col3 ml10'>到店在付:￥{{allShopMoney}}</view>
       </i-col>
       <i-col span="8" i-class="col-class" >
         <button  @click='goPay()' style='background:#ff6666;color:#fff;height:60px;line-height:60px;width:100%;' class='fs18 tc'>去支付</button>
       </i-col>
     </i-row>
   </view>
</view>
</template>
<script>
import api from '@/utils/api'
import wx from 'wx';
export default {
  data () {
    return {
      titleFlag: false,
      appointmentId: '',
      info: '',
      allAdvance: 0,
      allShopMoney: 0
    }
  },
  async mounted () {
    this.allAdvance = 0
    this.allShopMoney = 0
    if (this.$route.query.appointmentId) {
      this.appointmentId = this.$route.query.appointmentId;
    }
    await Promise.all([
      this.getInfo()
    ])
  },

  methods: {
    async getInfo (){
      wx.showLoading({
        title: '加载中',
      })
      const res = await api.getUserAppointInfoList({appointmentId:this.appointmentId});
        if (res.result === '0x00001'){
          this.info = res.responseData
          for(var i=0;i< this.info.shopProjectInfoResponseList.length;i++){
            this.info.shopProjectInfoResponseList[i].num = 1
            this.info.shopProjectInfoResponseList[i].shopMoney = 0
          }
        }
        this.allMoney ()
      },
      allMoney(){
        this.allAdvance = 0
        this.allShopMoney = 0
        for(var i=0;i<this.info.shopProjectInfoResponseList.length;i++){
           this.info.shopProjectInfoResponseList[i].shopMoney =  this.info.shopProjectInfoResponseList[i].num * this.info.shopProjectInfoResponseList[i].marketPrice-this.info.shopProjectInfoResponseList[i].advancePayment
           this.allAdvance += this.info.shopProjectInfoResponseList[i].advancePayment
           this.allShopMoney += this.info.shopProjectInfoResponseList[i].shopMoney
        }
        wx.hideLoading()
      },
      number (even,index) {
        if (even === 1) {
           this.info.shopProjectInfoResponseList[index].num = this.info.shopProjectInfoResponseList[index].num/1 + 1;
          } else if (even === -1) {
            if (this.info.shopProjectInfoResponseList[index].num === 1) {
               util.showErrorToast('不能再减少了');
              return
            }
           this.info.shopProjectInfoResponseList[index].num -= 1
          }
          this.allMoney ()
      },
      // 跳转支付
      goPay () {
        var that = this
        this.getOrderId()
      },
      //获取orderId
      async getOrderId () {
         var shopUserOrderDTO ={
           shopId: this.info.shopProjectInfoResponseList[0].sysShopId,
           shopUserProjectRelationDTOS: [],
           userName: this.userName,
           arriveShopPayment: this.allAdvance + this.allShopMoney,
           orderSource: '1',
           userShopAppointId: this.appointmentId
          }
        var projectList = this.info.shopProjectInfoResponseList
        for(var i=0;i<projectList.length;i++ ){
          shopUserOrderDTO.shopUserProjectRelationDTOS[i]={}
          shopUserOrderDTO.shopUserProjectRelationDTOS[i].functionIntr = projectList[i]. functionIntr
          shopUserOrderDTO.shopUserProjectRelationDTOS[i].projectUrl = projectList[i]. projectUrl
          shopUserOrderDTO.shopUserProjectRelationDTOS[i].serviceTime = projectList[i]. num
          shopUserOrderDTO.shopUserProjectRelationDTOS[i].sysShopProjectName = projectList[i]. projectName
          shopUserOrderDTO.shopUserProjectRelationDTOS[i].advancePayment = projectList[i].advancePayment
          shopUserOrderDTO.shopUserProjectRelationDTOS[i].arriveShopPayment  = projectList[i].shopMoney
          shopUserOrderDTO.shopUserProjectRelationDTOS[i].sysShopProjectPurchasePrice = projectList[i].marketPrice
          shopUserOrderDTO.shopUserProjectRelationDTOS[i].sysShopProjectId= projectList[i].id
        }
        const res =  await api.saveOrderInfoNew(shopUserOrderDTO)
        if(res.result === '0x00001'){
          wx.navigateTo({
            url: '/pages/businessCircle/pay?orderId=' + res.responseData.orderId + '&&allAdvance=' + this.allAdvance+'&&userShopAppointId=' + res.responseData.userShopAppointId + '&&allShopMoney=' + this.allShopMoney
          })
        }
      },
      showTitle (){
         this.titleFlag = true
      },
      hideTitle (){
         this.titleFlag = false
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
   .mb10{margin-bottom:10px;}
   .cold0{color:#d0d0d0}
   .col3{color:#333}
   .col6{color:#666}
   .btl{border-top:2rpx solid #d0d0d0;}
   .bbl{border-bottom:2rpx solid #d0d0d0;}
   .bgf{background:#fff}
</style>
