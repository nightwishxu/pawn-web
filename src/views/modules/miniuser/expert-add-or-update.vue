<template>
  <el-dialog :title="!dataForm.id ? $t('m.add') : $t('m.edit')" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="80px">
      <el-form-item label="用户名称">
        <el-input v-model="dataForm.userName" placeholder="请填写用户名称"></el-input>
      </el-form-item>
      <el-form-item label="手机号" prop="phone">
        <el-input v-model="dataForm.userPhone" placeholder="请输入手机号" disabled></el-input>
      </el-form-item>
    </el-form>
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
      // 数据列表
      options: [],
      disable: false,
      dataForm: {
        id: 0,
        name: '',
        password: '',
        comfirmPassword: '',
        phone: '',
        types: null,
        code: ''
      },
      dataRule: {}
    }
  },
  methods: {
    init(id) {
      this.dataForm.id = id || 0
      this.visible = true
      this.$http({
        url: this.$http.adornUrl('/appraisal/list'),
        method: 'get',
        params: this.$http.adornParams()
      }).then(({data}) => {
        this.options = data
      }).then(() => {
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
        })
      })

      if (this.dataForm.id) {
        this.$http({
          url: this.$http.adornUrl(`/mini/user/getById/${this.dataForm.id}`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataForm = {
              userName: data.data.name,
              userPhone: data.data.phone,
              id: data.data.id
            }
            // let types = []
            // for (let type of data.types) {
            //   types.push(type.appraisalTypeCode)
            // }
            // this.dataForm.types = types
          }
        })
      }
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs['dataForm'].validate(valid => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/mini/user/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              id: this.dataForm.id || undefined,
              name: this.dataForm.userName,
              phone: this.dataForm.userPhone
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
