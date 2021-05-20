<template>
  <el-dialog title="绘画鉴定详情" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" label-width="80px">
      <el-form-item label="正视图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.frontImg" :preview-src-list="[showURL + dataForm.frontImg]"></el-image>
      </el-form-item>
      <el-form-item label="题跋图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.postscriptImg" :preview-src-list="[showURL + dataForm.postscriptImg]"></el-image>
      </el-form-item>
      <el-form-item label="落款图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.settlementImg" :preview-src-list="[showURL + dataForm.settlementImg]"></el-image>
      </el-form-item>
      <el-form-item label="局部图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.localImg" :preview-src-list="[showURL + dataForm.localImg]"></el-image>
      </el-form-item>
      <el-form-item label="印鉴或签名图">
        <el-image style="width: 100px; height: 100px" :src="showURL + dataForm.autographImg" :preview-src-list="[showURL + dataForm.autographImg]"></el-image>
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
        frontImg: '',
        postscriptImg: '',
        settlementImg: '',
        localImg: '',
        autographImg: '',
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
