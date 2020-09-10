<template>
    <div class="course-video">
        <draggable v-model="videos" style="display: inline-block" @update="datadragEnd" :options="{animation:500}">
            <transition-group>
                <course-video-player class="video-item" v-for="item in videos" :key="item.vid" :info="item" @deleteHandler="deleteItem"/>
            </transition-group>
        </draggable>
        <div class="video-item">
            <el-upload
                    class="avatar-uploader"
                    style="width: 140px; margin: 80px auto;"
                    action=""
                    :http-request="uploadAvFileElment"
                    :show-file-list="false"
                    :on-success="handleAvSuccess"
                    :before-upload="beforeAvUpload">
                <img v-if="showUpload" style="width: 140px; height: 100px;" src="../../../assets/img/upload2.jpg">
                <img v-else style="width: 140px; height: 140px;" src="../../../assets/img/loading.gif">
                <!--                <el-progress v-else type="circle" :percentage="60" status="success"></el-progress>-->
            </el-upload>
        </div>

    </div>
</template>

<script>

  import CourseVideoPlayer from './course-video-player'
  import draggable from 'vuedraggable'
  import Cookies from 'js-cookie'

  export default {
    name: 'course-video',
    components: { CourseVideoPlayer, draggable },
    props: {
      dataForm: {}
    },
    data () {
      return {
        showUpload: true,
        videos: [],
        startArr: [],
        endArr: [],
        count: 0
      }
    },
    mounted () {
      //为了防止火狐浏览器拖拽的时候以新标签打开，此代码真实有效
      document.body.ondrop = function (event) {
        event.preventDefault()
        event.stopPropagation()
      }
    },
    methods: {
      getdata (evt) {
        console.log(evt.draggedContext.element.text)
      },
      datadragEnd (evt) {
        evt.preventDefault()
        console.log('拖动前的索引 :' + evt.oldIndex)
        console.log('拖动后的索引 :' + evt.newIndex)
        console.log(this.colors)
      },
      clean () {
        console.info('清除....')
        this.videos = []
      },

      deleteItem (vid) {
        console.info('删除: vid=' + vid)

        let items = []
        this.videos.forEach((v, i) => {
          if (v.vid !== vid) {
            items.push(v)
          }
        })
        this.videos = items
      },

      handleAvSuccess: function (res, file) {
        var item = { 'vid': res.data.info.fileId, 'name': '' }
        this.videos.push(item)
        this.showUpload = true
      },
      beforeAvUpload: function (file) {
        var isJPG = (file.type === 'video/mp4')
        var isLt2M = file.size / 1024 / 1024 < 800

        if (!isJPG) {
          this.$message.error('上传视频格式只能是mp4格式!')
        }
        if (!isLt2M) {
          this.$message.error('上传视频大小不能超过 800MB!')
        }
        return isJPG && isLt2M
      },
      uploadAvFileElment: function (content) {
        var form = new FormData()
        form.append('name', content.file.name)
        form.append('file', content.file)
        this.showUpload = false
        this.$http.post('/api/vod/upload?token=' + Cookies.get('token'), form, {
          timeout: 0,
          headers: { 'Content-Type': 'multipart/form-data' }
        }).then(function (res) {
          content.onSuccess(res)
        }, function (error) {
          content.onError(error)
        })
      },

    },

    watch: {

      // dataForm: {
      //   deep: true,
      //   handler (val) {
      //     if (val && val.videos) {
      //       this.videos = JSON.parse(val.videos)
      //     }
      //   }
      // },

      dataForm (val) {
        if (val && val.videos === '') {
          this.videos = []
        }
        if (val && val.videos) {
          this.videos = JSON.parse(val.videos)
        }
      }

    }
  }
</script>

<style scoped lang="scss" >
    .test {
        border: 1px solid #ccc;
    }

    .drag-item {
        width: 200px;
        height: 50px;
        line-height: 50px;
        margin: auto;
        position: relative;
        background: #ddd;
        margin-top: 20px;
    }

    .ghostClass {
        opacity: 1;
    }

    .bottom {
        width: 200px;
        height: 50px;
        position: relative;
        background: blue;
        top: 2px;
        left: 2px;
        transition: all .5s linear;
    }

    .video-item {
        margin: 5px;
        /*border: 1px solid #d8d8d8;*/
        border-radius: 3px;
        box-shadow: 0 2px 6px 0 rgba(0, 0, 0, .1);
        width: 280px;
        height: 250px;
        display: inline-block;
        overflow: hidden;
        cursor: move !important;

        &:hover {
            border: 2px solid #ddd;
        }
    }

</style>
