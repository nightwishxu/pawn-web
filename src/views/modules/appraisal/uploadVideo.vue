<template>
  <el-dialog title="上传视频" :close-on-click-modal="false" :visible.sync="visible">
    <el-row>
      <el-col :span="8">
        <div class="grid-content bg-purple">
          拆箱视频
          <el-upload
            class="avatar-uploader el-upload--text"
            :action="actionURL"
            :show-file-list="false"
            :on-success="cxHandleVideoSuccess"
            :before-upload="beforeUploadVideo"
            :on-progress="cxUploadVideoProcess"
          >
            <video style="width: 200px;" v-if="videoForm.cxVideo != '' && cxVideoFlag == false" :src="videoForm.cxVideo" class="avatar" controls="controls">
              您的浏览器不支持视频播放
            </video>
            <i v-else-if="videoForm.cxVideo == '' && cxVideoFlag == false" class="el-icon-plus avatar-uploader-icon"></i>
            <el-progress v-if="cxVideoFlag == true" type="circle" :percentage="cxVideoUploadPercent" style="margin-top:30px;"></el-progress>
          </el-upload>
        </div>
      </el-col>
      <el-col :span="8">
        <div class="grid-content bg-purple-light">
          鉴定视频
          <el-upload
            class="avatar-uploader el-upload--text"
            :action="actionURL"
            :show-file-list="false"
            :on-success="jdHandleVideoSuccess"
            :before-upload="beforeUploadVideo"
            :on-progress="jdUploadVideoProcess"
          >
            <video style="width: 200px;" v-if="videoForm.jdVideo != '' && jdVideoFlag == false" :src="videoForm.jdVideo" class="avatar" controls="controls">
              您的浏览器不支持视频播放
            </video>
            <i v-else-if="videoForm.jdVideo == '' && jdVideoFlag == false" class="el-icon-plus avatar-uploader-icon"></i>
            <el-progress v-if="jdVideoFlag == true" type="circle" :percentage="jdVideoUploadPercent" style="margin-top:30px;"></el-progress>
          </el-upload>
        </div>
      </el-col>
      <el-col :span="8">
        <div class="grid-content bg-purple">
          包装视频
          <el-upload
            class="avatar-uploader el-upload--text"
            :action="actionURL"
            :show-file-list="false"
            :on-success="bzHandleVideoSuccess"
            :before-upload="beforeUploadVideo"
            :on-progress="bzUploadVideoProcess"
          >
            <video style="width: 200px;" v-if="videoForm.bzVideo != '' && bzVideoFlag == false" :src="videoForm.bzVideo" class="avatar" controls="controls">
              您的浏览器不支持视频播放
            </video>
            <i v-else-if="videoForm.bzVideo == '' && bzVideoFlag == false" class="el-icon-plus avatar-uploader-icon"></i>
            <el-progress v-if="bzVideoFlag == true" type="circle" :percentage="bzVideoUploadPercent" style="margin-top:30px;"></el-progress>
          </el-upload>
        </div>
      </el-col>
    </el-row>
    <P class="text">请保证视频格式正确，且不超过10M</P>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">{{ $t('m.cancel') }}</el-button>
      <el-button type="primary" @click="dataFormSubmit()">{{ $t('m.save') }}</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      appraisalId: null,
      actionURL: '',
      cxVideoFlag: false,
      cxVideoUploadPercent: 0,
      jdVideoFlag: false,
      jdVideoUploadPercent: 0,
      bzVideoFlag: false,
      bzVideoUploadPercent: 0,
      videoForm: {
        cxVideo: '',
        jdVideo: '',
        bzVideo: '',
        cxVideoUploadId: '',
        jdVideoUploadId: '',
        bzVideoUploadId: ''
      }
    }
  },
  methods: {
    init(id) {
      let _this = this
      this.visible = true
      this.appraisalId = id
      this.actionURL = window.SITE_CONFIG.baseUrl + '/sys/file/upload'
      this.$http({
        url: this.$http.adornUrl(`/appraisal/get/video/${this.appraisalId}`),
        method: 'get',
        data: this.$http.adornParams()
      }).then(({ data }) => {
        if (data && data.code === 0) {
          _this.videoForm = {
            cxVideo: data.data.unpackingVideo === null ? '' : window.SITE_CONFIG.baseUrl + '/sys/file/show/' + data.data.unpackingVideo,
            jdVideo: data.data.appraisalVideo === null ? '' : window.SITE_CONFIG.baseUrl + '/sys/file/show/' + data.data.appraisalVideo,
            bzVideo: data.data.packingVideo === null ? '' : window.SITE_CONFIG.baseUrl + '/sys/file/show/' + data.data.packingVideo,
            cxVideoUploadId: data.data.unpackingVideo,
            jdVideoUploadId: data.data.appraisalVideo,
            bzVideoUploadId: data.data.packingVideo
          }
          console.log(_this.videoForm)
        } else {
          this.$message.error(data.msg)
        }
      })
    },
    dataFormSubmit() {
      this.$http({
        url: this.$http.adornUrl('/appraisal/save/video'),
        method: 'post',
        data: this.$http.adornData({
          id: this.appraisalId,
          cxVideoUploadId: this.videoForm.cxVideoUploadId,
          jdVideoUploadId: this.videoForm.jdVideoUploadId,
          bzVideoUploadId: this.videoForm.bzVideoUploadId
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 1500,
            onClose: () => {
              this.visible = false
              this.$emit('refreshDataList')
            }
          })
        } else {
          this.$message.error(data.msg)
        }
      })
    },
    beforeUploadVideo(file) {
      const isLt10M = file.size / 1024 / 1024 < 10
      if (['video/mp4', 'video/ogg', 'video/flv', 'video/avi', 'video/wmv', 'video/rmvb'].indexOf(file.type) === -1) {
        this.$message.error('请上传正确的视频格式')
        return false
      }
      if (!isLt10M) {
        this.$message.error('上传视频大小不能超过10MB哦!')
        return false
      }
    },
    cxUploadVideoProcess(event, file, fileList) {
      this.cxVideoFlag = true
      this.cxVideoUploadPercent = parseInt(file.percentage.toFixed(0))
    },
    jdUploadVideoProcess(event, file, fileList) {
      this.jdVideoFlag = true
      this.jdVideoUploadPercent = parseInt(file.percentage.toFixed(0))
    },
    bzUploadVideoProcess(event, file, fileList) {
      this.bzVideoFlag = true
      this.bzVideoUploadPercent = parseInt(file.percentage.toFixed(0))
    },
    cxHandleVideoSuccess(res, file) {
      this.cxVideoFlag = false
      this.cxVideoUploadPercent = 0
      if (res.code === 0) {
        this.videoForm.cxVideoUploadId = res.file_id
        this.videoForm.cxVideo = window.SITE_CONFIG.baseUrl + '/sys/file/show/' + res.file_id
      } else {
        this.$message.error('视频上传失败，请重新上传！')
      }
    },
    jdHandleVideoSuccess(res, file) {
      this.jdVideoFlag = false
      this.jdVideoUploadPercent = 0
      if (res.code === 0) {
        this.videoForm.jdVideoUploadId = res.file_id
        this.videoForm.jdVideo = window.SITE_CONFIG.baseUrl + '/sys/file/show/' + res.file_id
      } else {
        this.$message.error('视频上传失败，请重新上传！')
      }
    },
    bzHandleVideoSuccess(res, file) {
      this.bzVideoFlag = false
      this.bzVideoUploadPercent = 0
      if (res.code === 0) {
        this.videoForm.bzVideoUploadId = res.file_id
        this.videoForm.bzVideo = window.SITE_CONFIG.baseUrl + '/sys/file/show/' + res.file_id
      } else {
        this.$message.error('视频上传失败，请重新上传！')
      }
    }
  }
}
</script>
