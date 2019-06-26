<template >
<view class="container" style='background:#e6e6e6'>
<view style='width:100%;'>
   <swiper class="banner" indicator-dots="true" autoplay="true" interval="3000" duration="1000"  :style = 'heights' >
     <swiper-item v-for="item of imgUrls" :key="item" justify-content='center'  >
       <img :src="item" background-size="cover" style='width:100%;'  mode="widthFix" />
     </swiper-item>
   </swiper>
   <view style='padding:10px;' class='bgf'>
     <view class='fs17 col3 mt10 '>{{info.projectName}}</view>
     <view class='mt20'style='padding-bottom:10px;'>
       <i-row>
         <i-col span="12" i-class="col-class">
           <text style='font-size: 21px;' class='colff6 mr10'>¥{{info.marketPrice}}</text>
           <img src='/static/images/preferential.png' style='height:16px;width:16px;'class='mr10'/>
         </i-col>
         <i-col span="12" i-class="col-class">
           <view class=' fs15 tr mt5'>{{info.projectDuration}}分钟</view>
         </i-col>
       </i-row>
     </view>
     <view>
       <i-row>
         <i-col span="12" i-class="col-class">
           <text style='font-size: 16px;' class='col6'>门店价¥{{info. initialPrice}}</text>
         </i-col>
         <i-col span="12" i-class="col-class">
           <view class='fs15 tr mt5'>{{info.appointNumber}}人已经预约</view>
         </i-col>
       </i-row>
     </view>
   </view>
   <view class='mt10 '>
     <view v-if='sysShopList.length>=1'>
       <view class=' fs18 col3 bgf' style='height:30px;line-height:30px;padding-left:10px;'>推荐门店     </view>
         <view style='padding:10px 5px;' class='bgf'>
           <i-row @click='shopList'>
             <i-col span="6" i-class="col-class">
               <img :src='shop.zhaoxiangShopUrl' style='width:60px;height:60px;border-radius:50%;'/>
             </i-col>
             <i-col span="18" i-class="col-class">
               <view>
                 <i-row>
                   <i-col span="22" i-class="col-class">
                     <view class='fs10 '>{{shop.name}} </view>
                     <view class='cold0 fs17 mt10'>{{shop.address}}</view>
                   </i-col>
                   <i-col span="2" i-class="col-class">
                     <view class='mr10'> <i-icon type="enter" size='22' /></view>
                   </i-col>
                 </i-row>
               </view>
             </i-col>
            </i-row>
        </view>
     </view>

   <view style='margin-bottom:40px;margin-top:10px;padding-top:10px;' class='bgf'>
     <view style='height:40px;line-height:20px;padding:0 10px;'>
       <i-row>
         <i-col span="8" i-class="col-class" >
           <view class='fs18  tc' @click='changeTab(0)' :class="tagIndex == 0?'col57':'col3'">项目详情</view>
         </i-col>
         <i-col span="8" i-class="col-class" >
           <view class='fs18  tc' @click='changeTab(1)' :class="tagIndex == 1?'col57':'col3'">用户评价</view>
         </i-col>
         <i-col span="8" i-class="col-class" >
           <view class='fs18  tc' @click='changeTab(2)' :class="tagIndex == 2?'col57':'col3'">相关产品</view>
         </i-col>
       </i-row>
     </view>
     <view v-if='flag[0]'><img :src='info.projectDetailUrl' mode="widthFix" style='width:100%;'/></view>
     <view style='padding:10px 0;' class='bbl' v-for='(item,index) in shopUserCommentList' v-if='flag[1]'>
       <i-row>
         <i-col span="3" i-class="col-class" >
           <img :src='item.sysUserPhoto' style='border-radius:50%;width:26px;height:26px;margin-left:13px;'/>
         </i-col>
         <i-col span="21" i-class="col-class" >
           <view class='fs13 col3'>{{item.sysUserName}}</view>
           <view>
             <img src='/static/images/start.png' style='height:14px;width:14px;margin-right:5px;' v-for='(start,indexStaert) in item.commentGrade/1'>
           </view>
           <view class='fs13 col6'>{{item.content}}</view>
         </i-col>
       </i-row>
     </view>
     <view style='height:44px;line-height:44px;' class='fs16 colff6 tc mt10 mr10 bbl' @click='comment' v-if='flag[1]&&shopUserCommentList.length>0'>
        查看更多评价
        <i-icon type="unfold" size="20" color="#FF6666" />
      </view>
      <view v-if='flag[2]' style='backgroud:#fff'>
       <view style='padding:10px 5px;border-bottom:2rpx solid #d0d0d0;' v-for='(item,index) in relatedProject' @click='projectDetails(item.id,item.projectName)'>
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
                   <view class='fs14 tr'>{{item.address == null?'':item.address}}</view>
                 </i-col>
               </i-row>
             </view>
           </i-col>
          </i-row>
      </view>
   </view>
 </view>
</view>
 <view style='position:fixed;bottom:0;z-index:5;width:100%;background:#fff;' v-if='timeFlag'>
   <view class='fs18 bbl' style='padding:10px;'>服务时间</view>
   <view style="background: white;color:black;margin-top:1px;text-align: center;height:50px;line-height:50px;">
     <view   v-for="(item,index) in weekDays" :keys='index' @click="chooseAppointDate(item.dateValue)" style='width:14.285%;display:inline-block'>
        <span class="noChooseDay" v-if="item.dateValue!=chooseWeekDate">{{item.dateIndex}}</span>
        <span class="chooseDay" v-if="item.dateValue==chooseWeekDate">{{item.dateIndex}}</span>
     </view>
   </view>
   <div style="margin-top:0px;background:pink;color:transparent;font-size:1px;height:2px;"></div>
   <div style="background:white;color:black;font-size:10px;width:100%;line-height:25px;font-size:13px;margin-bottom:60px;" >
     <view>
       <view  style='display:inline-block;width:14.2%;text-align:center;' v-for="(item,indexTime) in timeDateShow" :keys='index' @click='selTime(indexTime)'>
         <view><span style="color: black" v-if="item.status=='0'">{{item.value}}</span>
         <span style="color:red;" v-if="item.status=='1'">{{item.value}}</span></view>
       </view>
     </view>
   </div>
   <view style='position:fixed;bottom:0;z-index:4;height:40px;line-height:40px;width:100%;' class='bgff6 tc fs16 colf' @click='trueAppointment()'>确认预约</view>
   </view>
</view>

  <view style='position:fixed;top:0px;width:100%;z-index:2; background:rgba(0,0,0,0.3);height:100%' v-if='timeFlag' @click='timeListHide()'></view>
 <view style='position:fixed;bottom:0px;width:100%;font-family: PingFang-SC-Medium;'>
   <i-row style='height:50px;line-height:50px;'>
     <i-col span="6" i-class="col-class"  >
       <view style='background: #FFBF4E;'class='colf fs16 tc'@click='calling()'> <img src='/static/images/pone.png' style='height:18px;width:18px;'class='mr5'/>电话咨询</view>
     </i-col>
     <i-col span="18" i-class="col-class" >
       <view class='bgff6 fs18 colf tc' @click='timeListShow'> 预约</view>
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
      timeFlag: false,
      imgUrls: [],
      id: '',
      name: '',
      info: '',
      shop: '',
      shopUserCommentList: [],
      commentNum: 5,
      sysShopList: [],
      flag: [true,false,false],
      tagIndex: 0,
      weekDays: [],
      timeDate: [],
      timeDateShow: [],
      phone: '',
      startTime: '',
      endTime: '',
      startIndex: 0,
      endIndex: 48,
      chooseWeekDate: '',// 选择的日期
      intervalNum: '',
      chooseTimeDate: '',
      pageSize: 1000,
      relatedProject: '',
      shopName: '',
      syshopId: '',
      winWid: '', // 屏幕宽度
      heights: ''

    }
  },
  async mounted () {
    this.winWid = wx.getSystemInfoSync().windowWidth;
    if (this.$route.query.id && this.$route.query.name) {
      this.id = this.$route.query.id;
      this.name = this.$route.query.name;
    }
    if (this.$route.query.syshopId ) {
      this.syshopId = this.$route.query.syshopId;
      this.phone = this.$route.query.phone;
      this.startTime = this.$route.query.openAndCloseDoorDate.substring(0,this.$route.query.openAndCloseDoorDate.indexOf('--'));
      this.endTime = this.$route.query.openAndCloseDoorDate.substring(this.$route.query.openAndCloseDoorDate.indexOf('--')+2, this.shopInfo.openAndCloseDoorDate.length);

    }
    this.startIndex = 0
    this.endIndex = 48
    this.timeFlag = false
    this.chooseWeekDate = ''
    this.initDate()
    this.initialTimeDate()
    await Promise.all([
      this.getProjectInfo()
    ])
    this.initDate()
  },

  methods: {
    initDate(){
      this.weekDays = []
      for(var i = 0; i<7; i++)
        {
          var dateValue = util.getAddDate(util.getNowFormatDate(),i);
          var dateIndex = util.getAddDateIndex(util.getNowFormatDate(),i);
          if(i===0)
          {
            dateIndex = '今天';
          }
          if(i===1)
          {
            dateIndex = '明天';
          }
          if(i===2)
          {
            dateIndex = '后天';
          }
          var value = {
            dateIndex : dateIndex,
            dateValue : dateValue
          };
          this.weekDays.push(Object.assign(value));
      }
     },
    initialTimeDate (){
      this.timeDate = [{index:'0',value:'00:00',status:'0'},{index:'1',value:'00:30',status:'0'},{index:'2',value:'01:00',status:'0'},{index:'3',value:'01:30',status:'0'},{index:'4',value:"02:00",status:'0'},{index:'5',value:'02:30',status:'0'},
{index:'6',value:'03:00',status:'0'},{index:'7',value:'03:30',status:'0'},{index:'8',value:'04:00',status:'0'},{index:'9',value:'04:30',status:'0'},{index:'10',value:"05:00",status:'0'},{index:'11',value:'05:30',status:'0'},
{index:'12',value:'06:00',status:'0'},{index:'13',value:'06:30',status:'0'},{index:'14',value:'07:00',status:'0'},{index:'15',value:'07:30',status:'0'},{index:'16',value:"08:00",status:'0'},{index:'17',value:'08:30',status:'0'},
{index:'18',value:'09:00',status:'0'},{index:'19',value:'09:30',status:'0'},{index:'20',value:'10:00',status:'0'},{index:'21',value:'10:30',status:'0'},{index:'22',value:"11:00",status:'0'},{index:'23',value:'11:30',status:'0'},
{index:'24',value:'12:00',status:'0'},{index:'25',value:'12:30',status:'0'},{index:'26',value:'13:00',status:'0'},{index:'27',value:'13:30',status:'0'},{index:'28',value:"14:00",status:'0'},{index:'29',value:'14:30',status:'0'},
{index:'30',value:'15:00',status:'0'},{index:'31',value:'15:30',status:'0'},{index:'32',value:'16:00',status:'0'},{index:'33',value:'16:30',status:'0'},{index:'34',value:"17:00",status:'0'},{index:'35',value:'17:30',status:'0'},
{index:'36',value:'18:00',status:'0'},{index:'37',value:'18:30',status:'0'},{index:'38',value:'19:00',status:'0'},{index:'39',value:'19:30',status:'0'},{index:'40',value:"20:00",status:'0'},{index:'41',value:'20:30',status:'0'},
{index:'42',value:'21:00',status:'0'},{index:'43',value:'21:30',status:'0'},{index:'44',value:'22:00',status:'0'},{index:'45',value:'22:30',status:'0'},{index:'46',value:"23:00",status:'0'},{index:'47',value:'23:30',status:'0'}
    ]
  },
    async getProjectInfo () {
      wx.showLoading({
        title: '加载中',
      })
      const res =  await api.getProjectInfoAndRelationShop({id: this.id, projectName: this.name, startLatitude: util.shopInfo.startLatitude, startLongitude: util.shopInfo.startLongitude});
        if (res.result === '0x00001'){
          this.info = res.responseData.shopProjectInfoResponseDTO
          this.shopUserCommentList = this.info.shopUserCommentList.slice(0,this.commentNum)
          this.imgUrls =  this.info.imageList
          var that = this
          wx.getImageInfo({
            src:this.imgUrls[0].replace('http','https'),
            success: function (res) {
               that.heights = 'height:'+res.height*that.winWid/res.width + 'px'
            }
          });
          if(res.responseData.sysShopList === null || typeof res.responseData.sysShopList === 'string'){
             this.sysShopList = []
          }else{
            this.sysShopList = res.responseData.sysShopList
          }
          if(res.responseData.sysShopList.length>=1)this.shop = res.responseData.sysShopList[0]

          this.timeDateNews()
          this.intervalNum = Math.ceil(this.info.projectDuration/30)
          this.getRelationProject()
          wx.hideLoading();
        }
    },
    // 评论
    comment () {
      this.commentNum += 5
      this.shopUserCommentList = this.info.shopUserCommentList.slice(0, this.commentNum)
      if(this.shopUserCommentList.length === this.info.shopUserCommentList.length){
        util.showErrorToast('没有更多了哦');
      }
    },
    // 跳转门店列表
    shopList (){
      util.shopInfo.shopList = this.sysShopList
      wx.navigateTo({
        url: '/pages/businessCircle/shopLis',
      })
    },
    // 可预约的的时间
    timeDateNews (){
      for(var i=0;i<this.timeDate.length;i++ ){
        if(this.timeDate[i].value === this.startTime.replace(/\s+/g,"").replace(/\：/g,":")){
          this.startIndex = i
        }
        if(this.timeDate[i].value === this.endTime.replace(/\s+/g,"").replace(/\：/g,":")){
          this.endIndex = i
        }
      }
       this.timeDateShow = this.timeDate.slice(this.startIndex,this.endIndex+1)
      },
    // 预约时间的展示与隐藏
    timeListShow () {
      if (wx.getStorageSync('smallRoutineUserLoginToken') === '') {
        util.checkResponseData('projectDetails')
        return
      }
      if(this.syshopId === ''){
        util.titTop('请先选择店铺')
        return
      }
      this.timeFlag = true
    },
    // 点击空白处隐藏预约时间列表
    timeListHide (){
       this.timeFlag = false
    },
    // 选择日期
    chooseAppointDate (dateValue){
      this.chooseWeekDate = dateValue;
    },
    // 选择时间
    selTime(num){
      this.initialTimeDate()
      this.timeDateNews()
      this.chooseTimeDate = this.timeDateShow[num].value
      var  dateSel = this.chooseWeekDate +' '+ this.chooseTimeDate
      var timeDateSel = dateSel.replace(/-/g,"/");//替换字符，变成标准格式
      var d2=new Date();//取今天的日期
      var d1 = new Date(Date.parse(timeDateSel));
      if(d1<d2){
        util.titTop('当前时间不可预约')
        return
      }
     for(var i=num;i<num+this.intervalNum;i++){
       this.timeDateShow[i].status = '1'
     }
    },
    // 确认预约
    async trueAppointment (){
      if(this.chooseWeekDate === ''){
         util.titTop('请先选择日期')
         return
      }
      if(this.chooseTimeDate === ''){
         util.titTop('请先选择预约时间')
         return
      }
      this.timeFlag = false
      var shopAppointServiceDTO = {
        shopProjectName: this.info.projectName,
        shopProjectId: this.info.id,
        status: '5',
        appointmentSource: '1',
        appointPeriod: this.info.projectDuration,
        appointStartTimeS: this.chooseWeekDate + ' ' + this.chooseTimeDate,
        sysShopId: this.syshopId
      }
      const res = await api.saveUserAppointInfo(shopAppointServiceDTO);
      if(res.result === '0x00001'){
        wx.navigateTo({
          url: '/pages/businessCircle/trueAppointment?appointmentId=' + res.responseData.appointmentId
        })
        this.syshopId = ''
      }else{
        util.titTop('预约失败')
      }
    },
    // 咨询
    calling (){
      if(this.syshopId === ''){
        util.titTop('请先选择店铺')
        return
      }
      wx.makePhoneCall({
        phoneNumber: this.phone,
        success:function(){
        },
        fail:function(){
        }
      })
    },
    // tab切换
    changeTab (index){
      this.tagIndex = index
      for(var i=0;i<this.flag.length;i++){
        this.flag[i] = false
      }
      this.flag[index] = true
   },
    // 相关产品
    async getRelationProject () {
      const res =  await api.getProjectInfoAndRelationProject({projectTypeTwoId:this.info.projectTypeTwoId, projectInfoId:this.info.id,pageSize:this.pageSize});
      if(res.result === '0x00001'){
        this.relatedProject = res.responseData
      }
    },
    // 跳转到项目详情页
    projectDetails (id,name){
      wx.navigateTo({
        url: '/pages/businessCircle/projectDetails?id='+id + '&&name=' + name,
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
 .ml10{margin-left:10px;}
 .mt20{margin-top:20px;}
 .mr10{margin-right:10px;}
 .cold0{color:#d0d0d0}
 .col3{color:#333}
 .col6{color:#666}
 .bbl{border-bottom:2rpx solid #d0d0d0;}
 .col57{color: #57D7E6}
 .bgff6{background:#ff6666}
 .bgf{background:#fff}
 .colf{color:#fff}
 span.noChooseDay {
   color: #333333;
   line-height: 30px;
   font-size: 14px;
   background: white;
   display: inline-block;
   height: 30px;
   width: 44px;
     }
  span.chooseDay {
     color: white;
     line-height: 44px;
     font-size: 14px;
     background: #ff6666;
     display: inline-block;
     height: 44px;
     width: 44px;
     border-radius: 20px 20px 20px 20px;
     box-shadow: 0 1px 3px 0 #FF6666;
  }
</style>
