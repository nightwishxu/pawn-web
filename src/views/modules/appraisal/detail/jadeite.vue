<template>
  <el-dialog title="翡翠玉石鉴定详情" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" label-width="100px">
      <el-form-item label="外观照片">
        <el-image
          v-for="(item, index) in dataForm.photos.split(',')"
          :key="index"
          style="width: 100px; height: 100px"
          :src="showURL + item"
          :preview-src-list="photosList"
        ></el-image>
      </el-form-item>
      <el-form-item label="视频"><video style="width: 200px;" :src="showURL + dataForm.video" class="avatar" controls="controls">您的浏览器不支持视频播放</video></el-form-item>
      <el-form-item label="翡翠颜色"><el-input v-model="dataForm.color" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="翡翠种水地"><el-input v-model="dataForm.type" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="尺寸"><el-input v-model="dataForm.size" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="备注"><el-input v-model="dataForm.remark" disabled="disabled"></el-input></el-form-item>
    </el-form>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      showURL: '',
      dataForm: {
        photos: '',
        video: '',
        color: '',
        type: '',
        size: '',
        remark: ''
      },
      photosList: []
    }
  },
  methods: {
    init(id) {
      this.showURL = window.SITE_CONFIG.baseUrl + '/sys/file/show/'
      this.visible = true
      let _this = this
      this.$http({
        url: this.$http.adornUrl('/appraisal/detail'),
        method: 'get',
        params: this.$http.adornParams({ id: id })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          _this.dataForm = data.data.info
          let photos = data.data.info.photos
          let photoArr = photos.split(',')
          for (let i = 0; i < photoArr.length; i++) {
            this.photosList.push(this.showURL + photoArr[i])
          }
        }
      })
    }
  }
}
</script>
