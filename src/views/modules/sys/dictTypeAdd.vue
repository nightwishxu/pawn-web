<template>
  <el-dialog title="$t('m.add')" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item :label="$t('m.uniqueCode')" prop="code">
        <el-input v-model="dataForm.code" :placeholder="$t('m.uniqueCode')"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.dict')+$t('m.name')" prop="name">
        <el-input v-model="dataForm.name" :placeholder="$t('m.dict')+$t('m.name')"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">{{$t('m.cancel')}}</el-button>
      <el-button type="primary" @click="dataFormSubmit()">{{$t('m.save')}}</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data() {
      return {
        visible: false,
        dataForm: {
          code: '',
          name: ''
        },
        dataRule: {
          code: [{
            required: true,
            message: 'ID' + this.$t('m.name') + this.$t('prompt.cannotBeEmpty'),
            trigger: 'blur'
          }],
          name: [{
            required: true,
            message: this.$t('m.dict') + this.$t('m.name') + this.$t('prompt.cannotBeEmpty'),
            trigger: 'blur'
          }]
        }
      }
    },
    methods: {
      init() {
        this.visible = true
        this.dataForm.code = ''
        this.dataForm.name = ''
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl('/sys/dictType/save'),
              method: 'post',
              data: this.$http.adornData({
                'code': this.dataForm.code,
                'name': this.dataForm.name
              })
            }).then(({
              data
            }) => {
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
          }
        })
      }
    }
  }
</script>
