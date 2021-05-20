<template>
  <el-dialog :title="!dataForm.id ? $t('m.add') : $t('m.edit')" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item :label="$t('m.roleName')" prop="roleName">
        <el-input v-model="dataForm.roleName" :placeholder="$t('m.roleName')"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.remark')" prop="remark">
        <el-input v-model="dataForm.remark" :placeholder="$t('m.remark')"></el-input>
      </el-form-item>
      <el-form-item size="mini" :label="$t('m.grant')">
        <el-tree :data="menuList" :props="menuListTreeProps" node-key="menuId" ref="menuListTree" :default-expand-all="true"
          show-checkbox></el-tree>
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
    treeDataTranslate
  } from '@/utils'
  export default {
    data() {
      return {
        visible: false,
        menuList: [],
        menuListTreeProps: {
          label: 'name',
          children: 'children'
        },
        dataForm: {
          id: 0,
          roleName: '',
          remark: ''
        },
        dataRule: {
          roleName: [{
            required: true,
            message: this.$t('m.role') + this.$t('m.name') + this.$t('prompt.cannotBeEmpty'),
            trigger: 'blur'
          }]
        },
        tempKey: -666666 // 临时key, 用于解决tree半选中状态项不能传给后台接口问题. # 待优化
      }
    },
    methods: {
      init(id) {
        this.dataForm.id = id || 0
        this.$http({
          url: this.$http.adornUrl('/sys/menu/list'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({
          data
        }) => {
          this.menuList = treeDataTranslate(data, 'menuId')
        }).then(() => {
          this.visible = true
          this.$nextTick(() => {
            this.$refs['dataForm'].resetFields()
            this.$refs.menuListTree.setCheckedKeys([])
          })
        }).then(() => {
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/sys/role/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({
              data
            }) => {
              if (data && data.code === 0) {
                this.dataForm.roleName = data.role.roleName
                this.dataForm.remark = data.role.remark
                var idx = data.role.menuIdList.indexOf(this.tempKey)
                if (idx !== -1) {
                  data.role.menuIdList.splice(idx, data.role.menuIdList.length - idx)
                }
                this.$refs.menuListTree.setCheckedKeys(data.role.menuIdList)
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
              url: this.$http.adornUrl(`/sys/role/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                roleId: this.dataForm.id || undefined,
                roleName: this.dataForm.roleName,
                remark: this.dataForm.remark,
                menuIdList: [].concat(this.$refs.menuListTree.getCheckedKeys(), [this.tempKey], this.$refs.menuListTree
                  .getHalfCheckedKeys())
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
