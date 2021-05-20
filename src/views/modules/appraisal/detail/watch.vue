<template>
  <el-dialog title="手表鉴定详情" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" label-width="80px">
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
      <el-form-item label="品牌"><el-input v-model="dataForm.brand" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="主体材质"><el-input v-model="dataForm.subjectMaterial" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="款式（男/女）"><el-input v-model="dataForm.style" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="机芯类型"><el-input v-model="dataForm.movementType" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="使用情况"><el-input v-model="dataForm.useCase" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="配件"><el-input v-model="dataForm.enclosure" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="配件照（多个）">
        <el-image
          v-for="(item, index) in dataForm.enclosureFile.split(',')"
          :key="index"
          style="width: 100px; height: 100px"
          :src="showURL + item"
          :preview-src-list="enclosureList"
        ></el-image>
      </el-form-item>
      <el-form-item label="购买价格"><el-input v-model="dataForm.buyPrice" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="购买时间"><el-input v-model="dataForm.buyTime" disabled="disabled"></el-input></el-form-item>
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
        brand: '',
        subjectMaterial: '',
        style: '',
        movementType: '',
        useCase: '',
        enclosure: '',
        enclosureFile: '',
        buyPrice: '',
        buyTime: '',
        remark: ''
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
        params: this.$http.adornParams({ id: id })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          _this.dataForm = data.data.info
          let photos = data.data.info.photos
          let photoArr = photos.split(',')
          for (let i = 0; i < photoArr.length; i++) {
            this.photosList.push(this.showURL + photoArr[i])
          }

          let enclosureFile = data.data.info.enclosureFile
          let enclosureArr = enclosureFile.split(',')
          for (let i = 0; i < enclosureArr.length; i++) {
            this.enclosureList.push(this.showURL + enclosureArr[i])
          }
        }
      })
    }
  }
}
</script>
