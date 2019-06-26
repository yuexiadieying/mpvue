<template >
<view class="container" >
   <view v-for='item in urlList'>
      <web-view  :src='item'></web-view>
   </view>

</view >
</template >
<script>
import api from '@/utils/api'
import wx from 'wx';
export default {
  data () {
    return {
      count:1,
      urlList:[]
    }
  },
  async mounted () {
    await Promise.all([
      this.getArticle()
    ])
  },

  methods: {
    async getArticle () {
      const res = await api.getContentUrlList({count: this.count});
      if (res.result === '0x00001') {
         this.urlList = res.responseData
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

</style>
