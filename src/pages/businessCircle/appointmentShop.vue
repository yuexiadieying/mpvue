<template >
<view class="container">
  <view style='width:100%;'>
   <view class="flex-wrp area" style="flex-direction:row;position:fixed;top:0px;width:100%;background:#fff;">
     <i-row style='width:100%' >
       <i-col span="5" i-class="col-class" >
         <view class="flex-item tc fs16" style='padding:10px 0;' @click='cityShow()'>
           {{city}}
           <i-icon type="unfold" color='' size='18'/>
        </view>
       </i-col>
       <i-col span="14" i-class="col-class" class=''>
         <view class='page_row' bindtap="suo">
           <view class="search">
             <view class="df search_arr" >
               <icon class="searchcion" size='20' type='search'></icon>
               <input class="fs16"  placeholder="请输入关键字" v-model='shopName'  />
             </view>
           </view>
          </view>
       </i-col>
       <i-col span="5" i-class="col-class" class=''>
           <view style='background:#ff6666;width:68px;height:35px;line-height:35px;border-radius:20px;margin-top:8px;text-align:center;color:#fff'  @click='getShopList()'>搜索</view>
     </i-col>
     </i-row>
   </view>
   <view style='position:fixed;top:40px;width:100%;background:#fff;z-index:2;' atchtouchmove='true' v-if='cityFlag'>
     <view class='area' style='height:100%;'>
       <i-row>
         <i-col span="12" i-class="col-class" >
           <scroll-view style='max-height:400px;' scroll-y=’true‘>
             <view v-for='(item,index) in  cityList'>
               <view style='text-align:center;line-height:42px;height:42px;' class='bbl brl'  :class="item.province == province ? 'colff6' : ''"  @click='selProvince(index,item.province)' >{{item.province}}</view>
             </view>
           </scroll-view>
       </i-col>
         <i-col span="12" i-class="col-class" class='projectIntroduction' >
           <scroll-view style='max-height:400px;' scroll-y=’true‘>
             <view>
               <view style='text-align:center;padding:10px;' class='bbl' v-for='(items,indexs) in cityList[provinceIndex].citys' @click='selCity(items)' :class="items == city ? 'colff6' : ''" >{{items}}
               </view>
             </view>
           </scroll-view>
         </i-col>
        </i-row>
     </view>
   </view>
   <view style='position:fixed;top:40px;width:100%;z-index:1; background:rgba(0,0,0,0.3);height:100%' v-if='cityFlag==true' @click='hideCity'></view>
   <view style='margin-top:50px;'>
     <view style='padding:10px 5px;border-bottom:2rpx solid #d0d0d0;' v-for='(item,index) in info' @click='aboutShop(item.id)'>
       <i-row >
         <i-col span="6" i-class="col-class">
           <img :src='item.zhaoxiangShopUrl' style='width:60px;height:60px;border-radius:50%;'/>
         </i-col>
         <i-col span="18" i-class="col-class">
           <view class='fs10 '>{{item.name}}</view>
           <view class='cold0 fs17 mt10'>营业时间:{{item.openAndCloseDoorDate}}</view>
           <view class='mt5'>
             <i-row >
                <i-col span="18" i-class="col-class">
                    <view style='' class='fs15 col6'>{{item.address}}</view>
                </i-col>
                 <i-col span="6" i-class="col-class">
                     <view style='text-align:right' class='fs15 col6 mr10'>{{item.distance}}km</view>
                 </i-col>
             </i-row >
           </view>
           <view class='mt5'>
             <img src='/static/images/start.png' style='height:14px;width:14px;margin-right:5px;' v-for='(start,indexStaert) in item.shopClass/1'>
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
      shopName:'',
      province:'',
      city:'',
      provinceIndex:'',
      cityFlag:false,
      cityList:[],
      info:''
    }
  },
  async mounted () {
    await Promise.all([
      this.getShopList()
    ])
  },

  methods: {
    async getShopList(){
      wx.showLoading({
        title: '加载中',
      })
      this.cityFlag = false
     const res =  await api.getAllSmallRoutineShopInfo({city:util.shopInfo.city,shopName:this.shopName,startLatitude:util.shopInfo.startLatitude,startLongitude:util.shopInfo.startLongitude});
      if (res.result === '0x00001'){
        wx.hideLoading()
        this.cityList = res.responseData.cityProvinceList
        this.info = res.responseData.sysShopDataList
        if(this.info.length<=0){
          util.titTop('空空如也~')
        }
      }
    },
    //城市的显示与隐藏
    cityShow (){
       this.cityFlag = !this.cityFlag
    },
    hideCity (){
      this.cityFlag = false
    },
     //选择省
    selProvince (index,type) {
      this.provinceIndex = index
      this.province = type
      util.shopInfo.province = this.province
      util.shopInfo.provinceIndex = this.provinceIndex
    },
     //选择市
    async selCity (index) {
      this.city = index
      util.shopInfo.city = index
      this.cityFlag = false
      this.getShopList()
    },

    //进入门店
    aboutShop (syshopId){
      wx.navigateTo({
       url: '/pages/businessCircle/aboutShop?syshopId='+syshopId,
      })
    }
  },
  onShow () {
    this.province = util.shopInfo.province
    this.city = util.shopInfo.city
    this.provinceIndex = util.shopInfo.provinceIndex
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
 .search{
     width: 90%;
   }
   .search_arr {
     border: 1px solid #d0d0d0;
     border-radius: 25px;
     margin-left: 40rpx;
     margin-top:10px;
   }
   .search_arr input{
     margin-left: 60rpx;
     height: 60rpx;
     border-radius: 5px;
   }
   .bc_text {
     line-height: 68rpx;
     height: 68rpx;
     margin-top: 34rpx;
   }

   .sousuo {
     margin-left: 15rpx;
     width: 15%;
     line-height: 150%;
     text-align: center;
     border: 1px solid #d0d0d0;
     border-radius: 25px;
   }
   .page_row{
     display: flex;
     flex-direction: row
   }
   .searchcion {
     margin: 10rpx 10rpx 10rpx 10rpx;
     position: absolute;
     left:206rpx;
     z-index: 2;
     width: 20px;
     height: 20px;
     text-align: center;
   }
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
