<template>
    <div>
        <video ref="videoPlayer" width="280" height="200" preload="auto" playsinline webkit-playsinline></video>
        <el-input size="small" v-model="info.name" placeholder="名称" style="margin-top: 4px; margin-left: 4px; width: 240px"></el-input>
        <i class="el-icon-delete delete-item" @click="deleteHandler" title="删除" ></i>
    </div>
</template>

<script>

  import Cookies from 'js-cookie'

  export default {
    name: 'course-video-player',

    props: {
      info: {
        vid: '',
        name: '',
      }
    },

    methods: {

      deleteHandler(){
        // 确认是否删除
          let that = this
          this.$confirm('确认删除该视频?', { 'handle': '删除' }, '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
          }).then(() => {
              that.$emit('deleteHandler', this.info.vid);
          })
      }

    },

    mounted () {
      // console.info('加载播放器', JSON.stringify(this.info, '', ' '))
      let fileId = this.info.vid
      let that = this
      this.$http.get('/api/vod/view?token=' + Cookies.get('token') + '&fileID=' + fileId).then(function (res) {
        if (res.data.code === 0) {
          // console.info('获取播放器信息: view: >>> ', res.data.info)
          TCPlayer(that.$refs.videoPlayer, res.data.info)
        } else {
          return this.$message.error(res.data.msg)
        }
      }, function (error) {
        return this.$message.error('错误')
      })
    },

  }
</script>

<style scoped>

    .delete-item {
        font-size: 20px;
        color: #f00;
        font-weight: bold;
        /* text-indent: 10px; */
        margin-left: 8px;
        cursor: pointer;
    }

</style>
