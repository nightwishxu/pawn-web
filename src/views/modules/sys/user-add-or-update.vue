<template>
  <el-dialog :title="!dataForm.id ? $t('m.add') : $t('m.edit')" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item :label="$t('m.userName')" prop="userName">
        <el-input v-model="dataForm.userName" :placeholder="$t('m.userName')"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.passWord')" prop="password" :class="{ 'is-required': !dataForm.id }">
        <el-input v-model="dataForm.password" type="password" :placeholder="$t('m.passWord')"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.comfirmPassword')" prop="comfirmPassword" :class="{ 'is-required': !dataForm.id }">
        <el-input v-model="dataForm.comfirmPassword" type="password" :placeholder="$t('m.comfirmPassword')"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.email')" prop="email">
        <el-input v-model="dataForm.email" :placeholder="$t('m.email')"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.phone')" prop="mobile">
        <el-input v-model="dataForm.mobile" :placeholder="$t('m.phone')"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.role')" size="mini" prop="roleIdList">
        <el-checkbox-group v-model="dataForm.roleIdList">
          <el-checkbox v-for="role in roleList" :key="role.roleId" :label="role.roleId">{{ role.roleName }}</el-checkbox>
        </el-checkbox-group>
      </el-form-item>
      <el-form-item :label="$t('m.dept')" prop="dept">
        <select-tree v-model="dataForm.dept" :options="options" :props="defaultProps" />
      </el-form-item>
      <el-form-item :label="$t('m.isLeader')" prop="isLeader">
        <el-switch
          v-model="dataForm.isLeader"
          active-color="#13ce66"
          inactive-color="#ff4949"
          active-value='1' inactive-value='0'>
          </el-switch>
        </el-switch>
      </el-form-item>
      <el-form-item :label="$t('m.status')" size="mini" prop="status">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">{{$t('m.prohibit')}}</el-radio>
          <el-radio :label="1">{{$t('m.normal')}}</el-radio>
        </el-radio-group>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">{{$t('m.cancel')}}</el-button>
      <el-button type="primary" @click="dataFormSubmit()">{{$t('m.save')}}</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import SelectTree from '@/components/select-tree/SelectTree.vue'
  import {
    isEmail,
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
      var validateEmail = (rule, value, callback) => {
        if (!isEmail(value)) {
          callback(new Error('邮箱格式错误'))
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
        roleList: [],
        // 数据默认字段
        defaultProps: {
          parent: 'parentId', // 父级唯一标识
          value: 'id', // 唯一标识
          label: 'name', // 标签显示
          children: 'children' // 子级
        },
        // 数据列表
        options: [],
        dataForm: {
          id: 0,
          userName: '',
          password: '',
          comfirmPassword: '',
          salt: '',
          email: '',
          mobile: '',
          roleIdList: [],
          status: 1,
          dept: "",
          isLeader: '0'
        },
        dataRule: {
          userName: [{
            required: true,
            message: this.$t('m.userName') + this.$t('prompt.cannotBeEmpty'),
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
          email: [{
            required: true,
            message: this.$t('m.eamil') + this.$t('prompt.cannotBeEmpty'),
            trigger: 'blur'
          }, {
            validator: validateEmail,
            trigger: 'blur'
          }],
          mobile: [{
            required: true,
            message: this.$t('m.phone') + this.$t('prompt.cannotBeEmpty'),
            trigger: 'blur'
          }, {
            validator: validateMobile,
            trigger: 'blur'
          }],
          dept: [{
            required: true,
            message: this.$t('prompt.select') + this.$t('m.dept'),
            trigger: 'change'
          }]
        }
      }
    },
    components: {
      SelectTree
    },
    methods: {
      init(id) {
        this.dataForm.id = id || 0
        this.$http({
          url: this.$http.adornUrl('/sys/role/select'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({
          data
        }) => {
          this.roleList = data && data.code === 0 ? data.list : []
        }).then(() => {
          this.visible = true
          this.$nextTick(() => {
            this.$refs['dataForm'].resetFields()
          })
        }).then(() => {
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/sys/user/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({
              data
            }) => {
              if (data && data.code === 0) {
                this.dataForm.userName = data.user.username
                this.dataForm.salt = data.user.salt
                this.dataForm.email = data.user.email
                this.dataForm.mobile = data.user.mobile
                this.dataForm.roleIdList = data.user.roleIdList
                this.dataForm.status = data.user.status
                this.dataForm.dept = data.user.dept
                this.dataForm.isLeader = data.user.isLeader
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate(valid => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/user/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                userId: this.dataForm.id || undefined,
                username: this.dataForm.userName,
                password: this.dataForm.password,
                salt: this.dataForm.salt,
                email: this.dataForm.email,
                mobile: this.dataForm.mobile,
                dept: this.dataForm.dept,
                status: this.dataForm.status,
                roleIdList: this.dataForm.roleIdList,
                isLeader: this.dataForm.isLeader
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
      },
      initDept() {
        let _this = this
        this.$http({
          url: this.$http.adornUrl('/sys/dept/list'),
          method: 'get',
          data: this.$http.adornData()
        }).then(({
          data
        }) => {
          if (data.code === 0) {
            _this.options = data.data
          }
        })
      }
    },
    created() {
      this.initDept()
    }
  }
</script>
