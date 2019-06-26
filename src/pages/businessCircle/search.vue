<template >
<view class="container" style='background:#e6e6e6'>
<view style='width:100%;'>
  <view class="flex-wrp area" style="flex-direction:row;position:fixed;top:0px;z-index:3;width:100%;background:#fff;">
    <i-row style='width:100%;padding-bottom:10px;' >
      <i-col span="5" i-class="col-class" >
        <view class="flex-item tc fs16" style='padding:10px 0;height:40px;' @click='cityShow()'>
          {{city}}
          <i-icon type="unfold" color='' size='18'/>
        </view>
      </i-col>
      <i-col span="14" i-class="col-class" class=''>
        <view class='page_row' bindtap="suo">
          <view class="search">
            <view class="df search_arr" >
              <icon class="searchcion" size='20' type='search'></icon>
                <input class="fs16" focus='true' placeholder="请输入项目名称" v-model='search'  @focus = 'focusFun'/>
            </view>
          </view>
        </view>
      </i-col>
      <i-col span="5" i-class="col-class" class=''>
           <view style='background:#ff6666;width:68px;height:35px;line-height:35px;border-radius:20px;margin-top:8px;text-align:center;color:#fff'  @click='getInfo()'>搜索</view>
     </i-col>
    </i-row>
    <view style='padding:0 10px;' v-if='keyFlag' >
       <view style='height:40px;line-height:40px;' class='fs18' >搜索发现</view>
       <view style='height:45px;line-height:45px;' class='col6 fs16 bbl' v-for='item in keyList' @click='keyGetInfo(item)'>{{item}}</view>
    </view>
  </view>
  <view style='position:fixed;top:50px;width:100%;z-index:1; background:rgba(0,0,0,0.3);height:100%' v-if='keyFlag' @click='blurFun'></view>
  <view style='position:fixed;top:50px;width:100%;background:#fff;z-index:2;' atchtouchmove='true' v-if='cityFlag'>
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
   <view style='margin-top:50px;margin-bottom:60px;' class='bgf'>
     <view  v-for='(item,index) in info' >
       <view style='padding:10px 5px;border-bottom:2rpx solid #d0d0d0;' v-for='(item1,index1) in item.shopProjectInfoList' @click='projectDetails(item1.id,item1.projectName)'>
         <i-row >
           <i-col span="8" i-class="col-class">
             <img :src='item1.projectUrl' style='width:100px;height:100px;'/>
           </i-col>
           <i-col span="16" i-class="col-class">
             <view class='fs16'>{{item1.projectName}}</view>
             <view class='fs16 mt10' style='height:30px;line-height:30px;'>{{item1.projectFeatures==null?'':item1.projectFeatures}}</view>
             <view class='mt20'>
               <i-row>
                 <i-col span="13" i-class="col-class">
                   <view><text class='colff6 fs17 mr10'>{{item1.marketPrice}}</text><text style='text-decoration:line-through' class='fs16'>{{item1.initialPrice}}</text></view>
                 </i-col>
                 <i-col span="11" i-class="col-class">
                   <view class='fs14 tr'>{{info.address}}</view>
                 </i-col>
               </i-row>
            </view>
          </i-col>
        </i-row>
     </view>
   </view>
  </view>
</view>
<view style='height:60px;background:#fff;position:fixed;bottom:0;width:100%;' class='tc btl'>
  <i-row >
    <i-col span="8" i-class="col-class" class=' mt10' @click='home()'>
      <i-icon type="homepage_fill" size='30' color='#666666' class='mt5'/>
      <view  class=' fs15'>首页</view>
    </i-col>
    <i-col span="8" i-class="col-class" class=' mt10' @click='appointment' >
      <i-icon type="time_fill" size='30' color='#666666' class='mt5'/>
      <view  class=' fs15'>门店预约</view>
    </i-col>
    <i-col span="8" i-class="col-class" class=' mt10' @click='mine'>
      <i-icon type="mine_fill" size='30' color='#666666' class='mt5'/>
       <view  class=' fs15'>我的</view>
    </i-col>
  </i-row>
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
      city: util.shopInfo.city,
      province: util.shopInfo.province,
      cityFlag: false,
      provinceIndex: util.shopInfo.provinceIndex,
      cityList: util.shopInfo.cityList,
      search: "",
      info: '',
      keyFlag: true,
      keyList: ['补水','美白']
    }
  },
  async mounted () {
    this.search = ''
    this.info = ''
    this.cityList =  util.shopInfo.cityList
    this.province = util.shopInfo.province
    this.city = util.shopInfo.city
    this.provinceIndex = util.shopInfo.provinceIndex
    await Promise.all([
    ])
  },

  methods: {
    // 跳转到项目详情页
    projectDetails (id,name) {
      wx.navigateTo({
        url: '/pages/businessCircle/projectDetails?id=' + id + '&&name=' + name,
      })
    },
    // 首页
    home () {
      wx.switchTab({
       url: '/pages/businessCircle/home',
      })
     },
    // 门店预约
    appointment () {
      wx.switchTab({
        url: '/pages/businessCircle/appointmentShop',
      })
    },
    // 我的
    mine () {
      wx.switchTab({
        url: '/pages/businessCircle/mine',
      })
    },
    // 城市的显示与隐藏
    cityShow () {
      this.cityFlag = !this.cityFlag
      this.keyFlag = false
    },
    // 点击空白处隐藏城市
    hideCity () {
      this.cityFlag = false
    },
    // 选择省
    selProvince (index,type) {
      this.provinceIndex = index
      this.province = type
      util.shopInfo.province = this.province
      util.shopInfo.provinceIndex = this.provinceIndex
    },
    // 选择市
    async selCity (index) {
      this.city = index
      util.shopInfo.city = index
      this.cityFlag = false
    },
    async getInfo () {
      this.cityFlag = false
      this.keyFlag = false
      if(this.search===''){
        util.titTop('请输入项目名称')
        return
    }
    const res =  await api.getSearchProjectList({city:this.city,projectBrandName:"S唯美召香",projectName:this.search});
      if (res.result === '0x00001' && res.responseData.length>0){
         this.info = res.responseData
      }else{
        util.titTop('空空如也~')
        this.info = []
      }
    },
    blurFun () {
      this.keyFlag = false
      console.log(this.keyFlag)
    },
    focusFun () {
     this.keyFlag = true
     this.cityFlag = false
     console.log(this.keyFlag)
    },
    keyGetInfo (item) {
      this.search = item
      this.keyFlag = false
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
    .tr{text-align:right}
    .fs12{font-size:12px;}
    .fs13{font-size:13px;}
    .fs14{font-size:14px;}
    .fs15{font-size:15px;}
    .fs16{font-size:16px;}
    .fs17{font-size:17px;}
    .fs18{font-size:18px;}
    .colff6{color:#ff6666}
    .mt5{margin-top:5px;}
    .mt10{margin-top:10px;}
    .mt20{margin-top:20px;}
    .mr10{margin-right:10px;}
    .ml10{margin-left:10px;}
    .bbl{border-bottom:2rpx solid #d0d0d0;}
    .btl{border-top:2rpx solid #d0d0d0;}
    .brl{border-right:2rpx solid #d0d0d0;}
    .bgf{background:#fff}
</style>
