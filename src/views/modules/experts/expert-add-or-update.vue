<template>
  <el-dialog :title="!dataForm.id ? $t('m.add') : $t('m.edit')" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="专家姓名" prop="name">
        <el-input v-model="dataForm.name" placeholder="请填写专家姓名"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password" :class="{ 'is-required': !dataForm.id }" >
        <el-input v-model="dataForm.password" type="password" placeholder="请输入密码" ></el-input>
      </el-form-item>
      <el-form-item label="确认密码" prop="comfirmPassword" :class="{ 'is-required': !dataForm.id }">
        <el-input v-model="dataForm.comfirmPassword" type="password" placeholder="请输入密码" ></el-input>
      </el-form-item>
      <el-form-item label="手机号" prop="phone">
        <el-input v-model="dataForm.phone" placeholder="请输入手机号"></el-input>
      </el-form-item>
      <el-form-item label="鉴定类型" prop="types">
        <el-select v-model="dataForm.types" placeholder="请选择鉴定类型" multiple style="width: 100%;">
            <el-option
              v-for="item in options"
              :key="item.code"
              :label="item.name"
              :value="item.code">
            </el-option>
          </el-select>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">{{$t('m.cancel')}}</el-button>
      <el-button type="primary" @click="dataFormSubmit()">{{$t('m.save')}}</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import {
    isMobile
  } from '@/utils/validate'
  export default {
    data() {
      var validatePassword = (rule, value, callback) => {
        if (!this.dataForm.id && !/\S/.test(value)) {
          callback(new Error('密码不能为空'))
        } else {
          callback()
        }
      }
      var validateComfirmPassword = (rule, value, callback) => {
        if (!this.dataForm.id && !/\S/.test(value)) {
          callback(new Error('确认密码不能为空'))
        } else if (this.dataForm.password !== value) {
          callback(new Error('确认密码与密码输入不一致'))
        } else {
          callback()
        }
      }
      var validateMobile = (rule, value, callback) => {
        if (!isMobile(value)) {
          callback(new Error('手机号格式错误'))
        } else {
          callback()
        }
      }
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
        dataRule: {
          name: [{
            required: true,
            message: "姓名不能为空",
            trigger: 'blur'
          }],
          password: [{
            validator: validatePassword,
            trigger: 'blur'
          }],
          comfirmPassword: [{
            validator: validateComfirmPassword,
            trigger: 'blur'
          }],
          phone: [{
            required: true,
            message: "手机号码不能为空",
            trigger: 'blur'
          }, {
            validator: validateMobile,
            trigger: 'blur'
          }],
          types: [{
            required: true,
            message: "请选择鉴定类型",
            trigger: 'change'
          }]
        }
      }
    },
    methods: {
      init(id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$http({
          url: this.$http.adornUrl('/appraisal/type/list'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          console.log(data)
          this.options = data.data
        }).then(() => {
          this.$nextTick(() => {
            this.$refs['dataForm'].resetFields()
          })
        })

        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/experts/getById/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
             if (data && data.code === 0) {
              this.dataForm.name = data.expert.name
              this.dataForm.phone = data.expert.phone
              this.dataForm.code = data.expert.code
              let types = []
              for (let type of data.types) {
                types.push(type.appraisalTypeCode)
              }
              this.dataForm.types = types
            }
          })
        }
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate(valid => {
          if (valid) {
            console.log(this.dataForm)
             this.$http({
              url: this.$http.adornUrl(`/experts/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                id: this.dataForm.id || undefined,
                name: this.dataForm.name,
                password: this.dataForm.password,
                phone: this.dataForm.phone,
                types: this.dataForm.types.join(","),
                code: this.dataForm.code
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
