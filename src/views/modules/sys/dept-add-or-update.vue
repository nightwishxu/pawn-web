<template>
  <el-dialog :title="title" :visible.sync="visible" ref="drawer" @close="closeForm">
    <div class="drawer__content el-card__body">
      <el-form ref="form" :model="form" label-width="120px" :rules="rules" :validate-on-rule-change="false">
        <el-form-item :label="$t('m.superior')+$t('m.dept')">
          <el-input v-model="form.pname" :disabled="true"></el-input>
          <el-input v-model="form.pid" v-if="false"></el-input>
        </el-form-item>
        <el-form-item :label="$t('m.dept')+$t('m.name')" prop="name">
          <el-input v-model="form.name" :placeholder="$t('m.dept')+$t('m.name')"></el-input>
        </el-form-item>
        <el-form-item :label="$t('m.dept')+$t('dept.sign')">
          <el-input v-model="form.activiti_sign" :placeholder="$t('m.dept')+$t('dept.sign')"></el-input>
        </el-form-item>
      </el-form>
    </div>
    <div class="drawer__footer el-card__body text-center">
      <el-button type="primary" @click="onSubmit('form')">{{$t('m.save')}}</el-button>
      <el-button @click="cancel">{{$t('m.cancel')}}</el-button>
    </div>
  </el-dialog>
</template>

<script>
  export default {
    name: 'deptAddOrUpdate',
    inject: ['restTable'],
    data() {
      return {
        visible: false,
        title: '',
        value: [],
        data: [],
        form: {
          id: '',
          pname: '',
          pid: '',
          name: '',
          activiti_sign: ''
        }
      }
    },
    computed: {
      rules() {
        return {
          name: [{
            required: true,
            message: this.$t('prompt.enter') + this.$t('m.dept') + ' ' + this.$t('m.name'),
            trigger: 'blur'
          }]
        }
      }
    },
    methods: {
      onSubmit(formName) {
        var _this = this
        this.$refs[formName].validate(valid => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl('/sys/dept/saveOrUpdate'),
              method: 'post',
              data: this.$http.adornData(_this.form)
            }).then(({
              data
            }) => {
              if (data.code === 0) {
                _this.restTable()
                _this.visible = false
              }
            })
          } else {
            return false
          }
        })
      },
      cancel() { // 取消按钮事件
        this.visible = false
      },
      closeForm() { // 弹出框关闭事件，重置验证 此处不能与cancel事件写在一起
        this.$refs.form.resetFields()
      },
      init(id, name, title, action) { // 弹出框打开事件
        this.title = title
        this.visible = true
        this.form.id = ""
        let _this = this
        if (action === "addSame") {
          this.$http({
            url: this.$http.adornUrl('/sys/dept/getParentInfo'),
            method: 'get',
            params: this.$http.adornParams({
              id: id
            })
          }).then(({
            data
          }) => {
            if (data.code === 0) {
              _this.form.pid = data.data == null ? '0' : data.data.id
              _this.form.pname = data.data == null ? '/' : data.data.name
            }
          })
        } else if (action === "addSub") {
          _this.form.pname = name
          _this.form.pid = id
        } else {
          let _this = this
          this.form.id = id
          this.$http({
            url: this.$http.adornUrl(`/sys/dept/getDeptInfo/${id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({
            data
          }) => {
            console.log(data)
            if (data.code === 0) {
              _this.form.pid = data.data == null ? '0' : data.data.parent_id
              _this.form.pname = data.data.pname == null ? '/' : data.data.pname
              _this.form.name = data.data == null ? '/' : data.data.name
             _this.form.activiti_sign = data.data.activiti_sign
            }
          })
        }
      }
    }
  }
</script>

<style></style>
