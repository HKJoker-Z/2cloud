<template>
  <div>
    <div style="display: flex; color: #666">
      <div style="width: 200px;  text-align: center; border-right: 1px solid #ddd; min-height: calc(100vh - 86px);">
<!--        第一部分-->
          <div style="padding: 10px 0;">
            <div @click="loadFiles(item.category)" class="category" style="padding: 10px 0" v-for="item in categoryList" :key="item.text"
                 :class="{ 'category-active' : category === item.category}">
            <i v-if="item.icon" :class="item.icon" style="margin-right: 5px"></i>
            <span>{{ item.text }}</span>
          </div>
        </div>
<!--        第二部分-->
        <div style="border-top: 1px solid #ddd; text-align: center; padding-top: 10px" >
          <div @click="loadFiles('share')" style="padding: 10px 0; display: flex; justify-content: center" class="category" :class="{ 'category-active' : category === 'share'}">
            <div style="width: 75px; text-align: left">
              <i class="el-icon-share" style="margin-right: 5px"></i>
              <span>我的分享</span>
            </div>
          </div>

          <div @click="loadFiles('trash')" style="padding: 10px 0; display: flex; justify-content: center" class="category" :class="{ 'category-active' : category === 'trash'}">
            <div style="width: 75px; text-align: left">
              <i class="el-icon-delete" style="margin-right: 5px"></i>
              <span>回收站</span>
            </div>
          </div>
        </div>
      </div>

      <div style="flex: 1">

        <Share :type-list="typeList" v-if="category === 'share'" />
        <Trash :type-list="typeList" v-if="category === 'trash'" />
        <Main :type-list="typeList"  v-if="category !== 'trash' && category !== 'share'" />

      </div>

    </div>
  </div>
</template>

<script>
import Main from "@/components/Main.vue"
import Trash from "@/components/Trash.vue";
import Share from "@/components/Share.vue";
export default {
  components:{
    Main,
    Trash,
    Share
  },

  data() {
    return {
      categoryList: [
        {"text": '全部文件', category: 'all', icon: ''},
        {"text": '图片', category: 'img', icon: 'el-icon-picture-outline'},
        {"text": '视频', category: 'video', icon: 'el-icon-video-play'},
        {"text": '压缩', category: 'zip', icon: 'el-icon-box'},
      ],
      typeList: [
        { text: 'mp3', icon: 'el-icon-mp3' },
        { text: 'mp4', icon: 'el-icon-mp4' },
        { text: 'jpg', icon: 'el-icon-jpg' },
        { text: 'jpeg', icon: 'el-icon-jpeg' },
        { text: 'png', icon: 'el-icon-png' },
        { text: 'pdf', icon: 'el-icon-pdf' },
        { text: 'docx', icon: 'el-icon-docx' },
        { text: 'txt', icon: 'el-icon-text' },
        { text: 'zip', icon: 'el-icon-zip' },
        { text: 'folder', icon: 'el-icon-folder' },
      ],
      user: JSON.parse(localStorage.getItem('xm-user') || '{}'),
      category: this.$route.query.category, //分类
      // folderId: this.$route.query.folderId, //folderId
    }
  },
  mounted() {

  },
  // methods：本页面所有的点击事件或者其他函数定义区
  methods: {
    loadFiles(category) {
     location.href = '/front/home?category=' + category;

    }
  }
}
</script>

<style scoped>
.category {
  cursor: pointer;
}
.category:hover {
  background-color: #f8f8f8;
  color: #000;
}
.category-active {
  color: #409EFF;
}
</style>