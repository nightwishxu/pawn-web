<template>
  <el-dialog title="鉴定结果" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="data" ref="data" label-width="80px">
      <template v-if="data.state==='3'">
        <el-form-item label="鉴定结果">
          无法鉴定
        </el-form-item>
      <el-form-item label="理由">
        <el-input v-model="data.reason" disabled="disabled"></el-input>
      </el-form-item>
      </template>
      <template v-if="data.state==='2'">
      <el-form-item label="名称">
        <el-input v-model="data.name" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="尺寸">
        <el-input v-model="data.size" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="重量">
        <el-input v-model="data.weight" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="主材质">
        <el-input v-model="data.mainMaterial" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="副材质">
        <el-input v-model="data.subMaterial" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="年代">
        <el-input v-model="data.years" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="其他">
        <el-input v-model="data.other" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="市场流通性">
        <el-input v-model="data.marketLiquidity" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="价值稳定性">
        <el-input v-model="data.valueStability" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="材质易损性">
        <el-input v-model="data.materialVulnerability" disabled="disabled"></el-input>
      </el-form-item>
      <el-form-item label="典当价格">
        <el-input v-model="data.pawnPrice" disabled="disabled"></el-input>
      </el-form-item>
      </template>
    </el-form>
  </el-dialog>
</template>

<script>
  export default {
    data() {
      return {
        visible: false,
        showURL: '',
        data: {},
        photosList: [],
        enclosureList: []
      }
    },
    methods: {
      init(id) {
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
            _this.data = data.data
          }
        })
      }
    }
  }
</script>
