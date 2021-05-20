<template>
  <el-dialog title="贵金属鉴定详情" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" label-width="80px">
      <el-form-item label="金属种类"><el-input v-model="dataForm.metalType" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="重量"><el-input v-model="dataForm.weight" disabled="disabled"></el-input></el-form-item>
      <el-form-item label="纯度"><el-input v-model="dataForm.purity" disabled="disabled"></el-input></el-form-item>
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
        metalType: '',
        weight: '',
        purity: '',
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
