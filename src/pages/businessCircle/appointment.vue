<template >
<view class="container">
   <view style='width:100%;'>
     <scroll-view style='white-space: nowrap;' scroll-x=’true‘>
       <view class=" tc fs16" style='padding:10px 20px;display:inline-block;border-radius:20px;margin-right:10px;' @click="ThreeTypeProject(TwoTypeProjectList[0].sysShopId,'S唯美召香','')" :class="projectTypeTwoId ==''?'sel':'selNo'">
         全部
       </view>
       <view class=" tc fs16" style='padding:10px 20px;display:inline-block;border-radius:20px;margin-right:10px;'  v-for='(item,index) in TwoTypeProjectList' @click="ThreeTypeProject(item.sysShopId,'',item.id)" :class="projectTypeTwoId ==item.id?'sel':'selNo'">
         {{item.projectTypeName}}
       </view>
     </scroll-view>
     <view style='margin-bottom:40px;'>
       <view style='padding:10px 5px;border-bottom:2rpx solid #d0d0d0;' v-for='(item,index) in ThreeTypeProjectList' @click='chooseProject(item.id,item)'>
          <i-row >
            <i-col span="6" i-class="col-class">
              <img :src='item.projectUrl' style='width:80px;height:80px;background:pink;'/>
            </i-col>
            <i-col span="16" i-class="col-class">
              <view class='fs16 mt10'>{{item.projectName}}</view>
              <view class='mt20'>
                <i-row>
                  <i-col span="13" i-class="col-class">
                  <view><text class='colff6 fs17 mr10'>￥{{item.marketPrice}}</text><text style='text-decoration:line-through' class='fs16'>￥{{item.initialPrice}}</text></view>
                 </i-col>
                 <i-col span="11" i-class="col-class">
                 </i-col>
                </i-row>
              </view>
            </i-col>
            <i-col span="2" i-class="col-class">
              <view style='margin-top:30px;'>
                <i-icon type="success" size='30' color='#333'  v-if="!flagList[index]"/>
                <i-icon type="success_fill" size='30' color='#ff6666'  v-if="flagList[index]"/>
              </view>
             </i-col>
           </i-row>
        </view>
     </view>
   </view>
   <view style='position:fixed;bottom:0;z-index:3;width:100%;background:#fff' v-if='timeFlag'>
     <view class='fs18 col3 ' style='padding:10px;'>服务时间</view>
     <view style="background: white;color:black;margin-top:1px;text-align: center;height:50px;line-height:50px;">
       <view   v-for="(item,index) in weekDays" :keys='index' @click="chooseAppointDate(item.dateValue)" style='width:14.285%;display:inline-block'>
         <span class="noChooseDay" v-if="item.dateValue!=chooseWeekDate">{{item.dateIndex}}</span>
         <span class="chooseDay" v-if="item.dateValue==chooseWeekDate">{{item.dateIndex}}</span>
       </view>
     </view>
     <div style="margin-top:0px;background:pink;color:transparent;font-size:1px;height:2px;"></div>
     <div style="background:white;color:black;font-size:10px;width:100%;line-height:25px;font-size:13px;margin-bottom:60px;" >
       <view>
         <view style='display:inline-block;width:14.2%;text-align:center;' v-for="(item,index) in timeDateShow" @click='selTime(index)'>
         <view><span style="color: black" v-if="item.status=='0'">{{item.value}}</span>
           <span style="color:red;text-decoration:underline" v-if="item.status=='1'">{{item.value}}</span></view>
         </view>
       </view>
     </div>
    <view style='position:fixed;bottom:0;z-index:4;height:40px;line-height:40px;width:100%;' class='bgff6 tc fs16 colf' @click='trueAppointment()'>确认预约</view>
  </view>
  <view style='position:fixed;top:0px;width:100%;z-index:2; background:rgba(0,0,0,0.3);height:100%' v-if='timeFlag' @click='timeListHide()'></view>
 <view style='height:40px;line-height:40px;position:fixed;bottom:0;width:100%;z-index:1' class='bgff6 tc colf' @click='timeListShow'>
   下一步
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
      syshopId: '',
      TwoTypeProjectList: [],
      ThreeTypeProjectList: [],
      size: 1000,
      projectTypeTwoId: '',
      ThreeTypeProjectIds: [],
      startTime: '',
      endTime: '',
      startIndex: 0,
      endIndex: 48,
      timeDate: [],
      timeDateShow: [],// 用于展示的时间
      weekDays: [],
      appointmentProjectList: [],
      chooseWeekDate: '',//选择的日期
      intervalNum: '',
      appointPeriod: 0,//预约时长,
      chooseTimeDate: '',
      flagList: []
    }
  },

  async mounted () {
    this.startIndex = 0
    this.endIndex = 48
    this.timeFlag = false
    this.timeDateShow = []
    this.weekDays = []
    this.appointmentProjectList = []
    this.chooseWeekDate = ''
    this.intervalNum = ''
    this.appointPeriod = 0
    this.chooseTimeDate = ''
    this.ThreeTypeProjectIds = []
    if (this.$route.query.id) {
      this.syshopId = this.$route.query.id;
    }
    if (this.$route.query.startTime&&this.$route.query.endTime) {
      this.startTime = this.$route.query.startTime;
      this.endTime = this.$route.query.endTime;
    }
    await Promise.all(
      this.tagLabel()
    )
  },
  methods: {
    initDate(){
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
    // 预约时间的展示与隐藏
    timeListShow(){
      this.weekDays = []
      this.initDate()
      this.initialTimeDate()
      this.timeList()
      var timeLongth = 0;
      for(var i=0;i<this.appointmentProjectList.length;i++){
        timeLongth  = this.appointmentProjectList[i].projectDuration +timeLongth
      }
      this.intervalNum = Math.ceil(timeLongth/30)
      this.appointPeriod = timeLongth
      this.timeFlag = true
    },
    //根据营业时间截取
    timeList(){
      for(var i=0;i<this.timeDate.length;i++ ){
        if(this.timeDate[i].value == this.startTime.replace(/\s+/g,"").replace(/\：/g,":")){
           this.startIndex = i
        }
        if(this.timeDate[i].value == this.endTime.replace(/\s+/g,"").replace(/\：/g,":")){
            this.endIndex = i
        }
      }
      this.timeDateShow = this.timeDate.slice(this.startIndex,this.endIndex+1)
    },
    //选择时间
    selTime (index){
      this.initialTimeDate()
      this.timeList()
      if(this.ThreeTypeProjectIds.length <=0){
        util.titTop('请先选择预约项目')
        return
      }
      this.chooseTimeDate = this.timeDateShow[index].value
      var  dateSel = this.chooseWeekDate +' '+ this.chooseTimeDate
      var timeDateSel = dateSel.replace(/-/g,"/");// 替换字符，变成标准格式
      var d2=new Date();//取今天的日期
      var d1 = new Date(Date.parse(timeDateSel));
      if(d1<d2){
        util.titTop('当前时间不可预约')
        return
      }
      for(var i=index;i<index+this.intervalNum;i++){
        this.timeDateShow[i].status = '1'
      }
    },
    //tag标签
     async tagLabel (){
       wx.showLoading({
          title: '加载中',
       })
       const res =  await api.getAppointTwoTypeProject({sysShopId:this.syshopId, projectTypeName:'S唯美召香'});
       if(res.result === '0x00001'){
         wx.hideLoading({ })
         if(res.responseData === null){
            this.TwoTypeProjectList =[]
            this.ThreeTypeProjectList = []
            return
         }
         this.TwoTypeProjectList = res.responseData
         this.ThreeTypeProject(this.TwoTypeProjectList[0].sysShopId,'S唯美召香','')

       }
     },
     //根据二级id获取三级列表
    async ThreeTypeProject (sysShopId,projectTypeName,projectTypeTwoId){
      wx.showLoading({
         title: '加载中',
      })
      this.projectTypeTwoId = projectTypeTwoId
      const res =  await api.getAppointProjectByTwoType({sysShopId:sysShopId,projectTypeName:projectTypeName,projectTypeTwoId:projectTypeTwoId,pageSize:this.size});
      wx.hideLoading()
      if(res.result === '0x00001'){
        if(res.responseData === null){
          this.ThreeTypeProjectList =[]
          return
        }
        this.ThreeTypeProjectList = res.responseData
        for(var i=0;i<this.ThreeTypeProjectList.length;i++){
           this.flagList[i] = false
        }
      }else{this.ThreeTypeProjectList = []}
    },
    // 点击空白处隐藏预约时间列表
    timeListHide (){
      this.timeFlag = false
    },
    // 确认预约
    async trueAppointment (){
      if(this.ThreeTypeProjectIds.length<=0){
        util.titTop('请先选择预约项目')
        return
      }
      if(this.chooseWeekDate === ''){
        util.titTop('请先选择日期')
        return
      }
      if(this.chooseTimeDate === ''){
        util.titTop('请先选择预约时间')
        return
      }
      this.timeFlag = false
      var projectName = []
      for(var i=0;i<this.appointmentProjectList.length;i++){
        projectName[i] = this.appointmentProjectList[i].projectName
      }
      var shopAppointServiceDTO = {
        shopProjectName : projectName.join(';'),
        shopProjectId : this.ThreeTypeProjectIds.join(';'),
        status:'5',
        appointmentSource:'1',
        appointPeriod:this.appointPeriod,
        appointStartTimeS:this.chooseWeekDate +' ' + this.chooseTimeDate,
        sysShopId:this.appointmentProjectList[0].sysShopId
      }
      const res =  await api.saveUserAppointInfo(shopAppointServiceDTO);
      if(res.result === '0x00001'){
        wx.navigateTo({
          url: '/pages/businessCircle/trueAppointment?appointmentId='+ res.responseData.appointmentId
        })
      }else{
         util.titTop('预约失败')
      }
    },
     // 选择日期
    chooseAppointDate (dateValue){
      this.chooseWeekDate = dateValue;
    },
    // 选择项目
    chooseProject(projectId,item){
      if(this.ThreeTypeProjectIds.indexOf(projectId)!=-1){
        var key = 0;
        this.ThreeTypeProjectIds.forEach((val,index)=>{
          if(val===projectId){
             this.ThreeTypeProjectIds.splice(key,1);
             this.appointmentProjectList.splice(key,1);
          }
          key++;
        })
      } else {
        this.ThreeTypeProjectIds.push(projectId);
        this.appointmentProjectList.push(item);
      }
      for(var i=0;i<this.ThreeTypeProjectList.length;i++){
        this.flagList[i] = false
        for(var j=0;j<this.ThreeTypeProjectIds.length;j++){
          if(this.ThreeTypeProjectList[i].id === this.ThreeTypeProjectIds[j]){
          this.flagList[i] = true
          }
        }
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
 .colf{color:#fff}
 .mt5{margin-top:5px;}
 .mt10{margin-top:10px;}
 .mt20{margin-top:20px;}
 .mr10{margin-right:10px;}
 .cold0{color:#d0d0d0}
 .col3{color:#333}
 .col6{color:#666}
 .bbl{border-bottom:2rpx solid #d0d0d0;}
 .sel{border:2rpx solid #ff6666;color:#ff6666}
 .selNo{border:2rpx solid #d0d0d0;color:#333}
 .bgff6{background:#ff6666}
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
