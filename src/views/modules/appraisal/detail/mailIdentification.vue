<template>
  <el-dialog title="邮寄鉴定详情" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" label-width="80px">
      <el-form-item label="外观照片">
        <el-image v-for="(item, index) in dataForm.photos.split(',')" :key="index" style="width: 100px; height: 100px"
          :src="showURL + item" :preview-src-list="photosList"></el-image>
      </el-form-item>
      <el-form-item label="视频"><video style="width: 200px;" :src="showURL + dataForm.video" class="avatar" controls="controls">您的浏览器不支持视频播放</video></el-form-item>
      <el-form-item label="快递公司">
        <el-input v-model="dataForm.expressCompany" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="快递单号">
        <el-input v-model="dataForm.expressCode" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="保价金额">
        <el-input v-model="dataForm.money" disabled="disabled"></el-input>
      </el-form-item>
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
          expressCompany: '',
          expressCode: '',
          money: ''
        },
        photosList: [],
        enclosureList: []
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
          params: this.$http.adornParams({
            id: id
          })
        }).then(({
          data
        }) => {
          if (data && data.code === 0) {
            console.log(data)
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
