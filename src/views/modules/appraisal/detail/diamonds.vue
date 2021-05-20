<template>
  <el-dialog title="钻石鉴定详情" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" label-width="80px">
      <el-form-item label="形状">
		<el-input v-model="dataForm.shape" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="克拉">
		<el-input v-model="dataForm.carat" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="颜色">
		<el-input v-model="dataForm.color" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="净度">
		  <el-input v-model="dataForm.clarity" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="切工">
		<el-input v-model="dataForm.cut" disabled="disabled"></el-input>
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
        shape: '',
        carat: '',
        color: '',
        clarity: '',
        cut: '',
        remark: ''
      }
    }
  },
  methods: {
    init(id) {
      this.visible = true
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
