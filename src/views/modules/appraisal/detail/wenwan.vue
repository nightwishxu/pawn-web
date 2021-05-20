<template>
  <el-dialog title="文玩杂项鉴定详情" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" label-width="80px">
      <el-form-item label="整体图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.wholeImg" :preview-src-list="[showURL + dataForm.wholeImg]"></el-image>
      </el-form-item>
      <el-form-item label="45度图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.angleImg" :preview-src-list="[showURL + dataForm.angleImg]"></el-image>
      </el-form-item>
      <el-form-item label="侧面图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.sideImg" :preview-src-list="[showURL + dataForm.sideImg]"></el-image>
      </el-form-item>
      <el-form-item label="局部图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.localImg" :preview-src-list="[showURL + dataForm.localImg]"></el-image>
      </el-form-item>
      <el-form-item label="背面图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.backImg" :preview-src-list="[showURL + dataForm.backImg]"></el-image>
      </el-form-item>
      <el-form-item label="底面图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.bottomImg" :preview-src-list="[showURL + dataForm.bottomImg]"></el-image>
      </el-form-item>
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
        wholeImg: '',
        angleImg: '',
        sideImg: '',
        localImg: '',
        backImg: '',
        bottomImg: '',
        remark: ''
      }
    }
  },
  methods: {
    init(id) {
      this.visible = true
      this.showURL = window.SITE_CONFIG.baseUrl + '/sys/file/show/'
      this.$http({
        url: this.$http.adornUrl('/appraisal/detail'),
        method: 'get',
        params: this.$http.adornParams({ id: id })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataForm = data.data.info
        }
      })
    }
  }
}
</script>
