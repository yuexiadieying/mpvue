<template >
<view class="container" style='background:#e6e6e6'>
  <view style='height:40px;width:100%;color:gray;font-size:12px;position:fixed;z-index:3;background:#f6f6f6'>
       <i-row>
         <i-col span="16" i-class="col-class">
            <view style='margin:3px 10px;'>
            如果您的定位功能已授权给微信，点击可重新定位
            </view>
         </i-col>
         <i-col span="8" i-class="col-class">
            <button  @click='handler()' style='font-size:12px;width:76px;margin-top:5px;height:30px;color:#ff6666'>重新定位</button>
         </i-col>
       </i-row>
  </view>
  <view class="flex-wrp area" style="flex-direction:row;position:fixed;top:40px;z-index:3;width:100%;background:#fff;">
    <i-row style='width:100%' >
       <i-col span="6" i-class="col-class" >
          <view class="flex-item tc fs16" style='padding:10px 0;' @click='cityShow()'>
             {{city}}
             <i-icon type="unfold" color='' size='18'/>
          </view>
       </i-col>
       <i-col span="18" i-class="col-class" class=''>
          <view class='page_row' bindtap="suo">
               <view class="search">
                 <view class="df search_arr" @click='search'>
                   <icon class="searchcion" size='20' type='search'></icon>
                   <input class="fs16" disabled placeholder="请输入关键字" />
                 </view>
               </view>
            </view>
       </i-col>
    </i-row>
  </view>
  <view style='position:fixed;top:82px;width:100%;background:#fff;z-index:2;' atchtouchmove='true' v-if='cityFlag'>
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
 <view style='position:fixed;top:82px;width:100%;z-index:1; background:rgba(0,0,0,0.3);height:100%' v-if='cityFlag==true' @click='hideCity'></view>
  <view style='padding:0 10px;width:100%;' class='bgf'>
     <swiper class="banner " indicator-dots="true" autoplay="true" interval="3000" duration="1000" style='width:100%;margin-top:90px;;' :style = 'heights'>
       <swiper-item v-for="item of imgUrls" :key="item" justify-content='center'>
         <img :src="item" style='width:100%;'  mode="widthFix"/>
       </swiper-item>
     </swiper>

  </view>
   <view style='margin-top:10px;width:100%;' class='bgf'>
      <view style='padding:10px;'>明星推荐</view>
      <scroll-view style='white-space: nowrap;' scroll-x='true' class=''>
          <view class="flex-item tc fs16" style="height:150px;width:250px;display:inline-block;margin-left:10px;" @click='projectDetails(item.id,item.projectName)' v-for='(item,index) in homeInfo.recommendProjectResponseDTOList' >
             <img :src='item.recommendUrl' style='width:100%;height:100%;'/>
          </view>
      </scroll-view>
   </view>
  <view style='width:100%;' class='bgf  mt10'>
     <view style='padding:10px;'>精选推荐</view>
     <navigator url='article' class=''>
        <img src='http://mx-beauty.oss-cn-beijing.aliyuncs.com/smallRoutine/%E7%B2%BE%E9%80%89%E6%8E%A8%E8%8D%90-%E6%97%A0%E9%92%88%E6%B0%B4%E5%85%89.png' style='width:100%;height:130px;'>
     </navigator>
  </view>
  <view class='mt10 bgf' style='width:100%;'>
    <view class='fs18' style='padding:10px;'>爆款推荐</view>
    <view style='padding:10px 5px;border-bottom:2rpx solid #d0d0d0;' v-for='(item,index) in homeInfo.recommendProjectResponseDTOList' @click='projectDetails(item.id,item.projectName)'>
       <i-row >
          <i-col span="8" i-class="col-class">
            <img :src='item.projectUrl' style='width:90px;height:90px;'/>
          </i-col>
          <i-col span="16" i-class="col-class">
            <view class='fs16'>{{item.projectName}}</view>
            <view class='fs16 mt10' style='height:30px;line-height:30px;'>{{item.projectFeatures==null?'':item.projectFeatures}}</view>
            <view class=''>
              <i-row>
                <i-col span="13" i-class="col-class">
                 <view><text class='colff6 fs20 mr10'>¥{{item.marketPrice}}</text><text style='text-decoration:line-through' class='fs16'>¥{{item.initialPrice}}</text></view>
                </i-col>
                <i-col span="11" i-class="col-class">
                  <view class='fs14 tr'>{{item.address}}</view>
                </i-col>
              </i-row>
            </view>
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
      imgUrls: ['https://mx-beauty.oss-cn-beijing.aliyuncs.com/smallRoutine/%E9%A6%96%E5%9B%BE2.jpg','http://mx-beauty.oss-cn-beijing.aliyuncs.com/smallRoutine/%E9%A6%96%E5%9B%BE3.jpg','http://mx-beauty.oss-cn-beijing.aliyuncs.com/smallRoutine/%E9%A6%96%E5%9B%BE4.jpg'],
      cityFlag: false,
      city: util.shopInfo.city,
      province: util.shopInfo.province,
      startLatitude: '',
      startLongitude: '',
      homeInfo: '',
      provinceIndex: util.shopInfo.provinceIndex,
      cityList: util.shopInfo.cityList,
      heights:'',
      winWid:''
    }
  },
    async mounted () {
      this.winWid = wx.getSystemInfoSync().windowWidth;
      var that = this
      wx.getImageInfo({
        src:this.imgUrls[0],
        success: function (res) {
          that.heights = 'height:'+res.height*that.winWid/res.width + 'px'
        }
      });
    await Promise.all([
      this.getongitudeLAndlatitude()
    ])
  },
  methods: {
    // 跳转到搜索页
    search () {
      wx.showTabBar({})
      this.cityFlag = false
      wx.navigateTo({
        url: '/pages/businessCircle/search'
      })
    },
    // 跳转到项目详情页
    projectDetails (id, name) {
      wx.navigateTo({
        url: '/pages/businessCircle/projectDetails?id=' + id + '&&name=' + name
      })
    },
    // 城市的显示与隐藏
    cityShow () {
      this.cityFlag = !this.cityFlag
    },
    // 点击空白处隐藏城市
    hideCity () {
      this.cityFlag = false
    },
    // 重新定位
    handler () {
      var that = this
      wx.openSetting({
        success: function () {
          that.getongitudeLAndlatitude()
        },
        fail: function (res) {}})
    },
    // 获取经纬度
    getongitudeLAndlatitude () {
      let that = this
      wx.getLocation({
        type: 'gcj02', // 返回可以用于wx.openLocation的经纬度
        altitude: true,
        success: function (res) {
          that.startLatitude = res.latitude
          that.startLongitude = res.longitude
          util.shopInfo.startLatitude = res.latitude
          util.shopInfo.startLongitude = res.longitude
          that.loadCity()
        },
        fail: function (res) {
          that.loadIP()
        }
      })
    },
    // 根据经纬度获取城市
    async loadCity () {
      wx.showLoading({
        title: '加载中'
      })
      const res = await api.getLatAndlngToCity({latitude: this.startLatitude, longitude: this.startLongitude});
      if (res.result === '0x00001') {
        this.city = res.responseData.city
        this.province = res.responseData.province
        this.getInfo()
      }
    },
    // 根据IP获取经纬度
    async loadIP () {
      wx.showLoading({
        title: '加载中'
      })
      const res = await api.ipToLongitudeAndLatitude({});
      if (res.result === '0x00001') {
        this.startLatitude = res.responseData.latitude
        this.startLongitude = res.responseData.longitude
        util.shopInfo.startLatitude = res.responseData.latitude
        util.shopInfo.startLongitude = res.responseData.longitude
        this.city = res.responseData.city
        this.province = res.responseData.province
      } else {
        this.city = this.city
        this.province = this.province
        this.startLongitude = ''
        this.startLatitude = ''
      }
      this.getInfo()
    },
    // 首页数据
    async getInfo () {
      const res = await api.getProjectByCityAndProjectName({city: this.city, projectBrandName: 'S唯美召香'});
      if (res.result === '0x00001') {
        this.homeInfo = res.responseData
        this.cityList = res.responseData.cityProvinceList
        util.shopInfo.cityList = this.homeInfo.cityProvinceList
        for (var i = 0; i < this.cityList.length; i++) {
          if (this.cityList[i].province.indexOf(this.province) !== -1) {
            this.provinceIndex = i
          }
        }
        wx.hideLoading()
        for (i = 0; i < this.homeInfo.recommendProjectResponseDTOList.length; i++) {
          this.homeInfo.recommendProjectResponseDTOList[i].projectFeatures = this.homeInfo.recommendProjectResponseDTOList[i].projectFeatures.substring(0, 12) + '...'
        }
      }
    },
    // 选择省
    selProvince (index, type) {
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
      this.getInfo()
    }
  },
  onShow(){
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
     left:235rpx;
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
   .brl{border-right:2rpx solid #d0d0d0;}
   .bgf{background:#fff}
</style>
