<template>
  <el-dialog title="选择专家" :close-on-click-modal="false" :visible.sync="visible">
    <el-tree :data="menuList" :props="menuListTreeProps" node-key="code" ref="menuListTree" :default-expand-all="true"
      @check-change="checkChange" show-checkbox></el-tree>
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
        menuList: [],
        menuListTreeProps: {
          label: 'name',
          children: 'children'
        },
        appraisalId: null,
        checkCode: [],
        tempKey: -666666 // 临时key, 用于解决tree半选中状态项不能传给后台接口问题. # 待优化
      }
    },
    methods: {
      init(id) {
        this.visible = true
        this.appraisalId = id
        this.$http({
          url: this.$http.adornUrl('/experts/expertTree'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({
          data
        }) => {
          this.menuList = data.data
        })
      },
      // 表单提交
      dataFormSubmit() {
        let idArr = this.$refs.menuListTree.getCheckedKeys()
        this.$http({
          url: this.$http.adornUrl('/experts/distribution'),
          method: 'post',
          data: this.$http.adornData({
            appraisalId: this.appraisalId,
            ids: idArr.join(",")
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
      },
      checkChange(a, b, c) {
        if (a.isType) {
          return
        }
        let arr = this.fuzzyQuery(this.$refs.menuListTree.getCheckedKeys(), a.expert_code, a.code)
        console.log(arr)
        if (arr.length > 0) {
          this.$message.error("你已经选择姓名为：" + a.name + "的专家")
          this.$refs.menuListTree.setChecked(a.code, false, false)
        }
      },
      fuzzyQuery(list, keyWord, code) {
        var arr = []
        for (var i = 0; i < list.length; i++) {
          if (list[i].indexOf(keyWord) >= 0 && list[i] !== code) {
            arr.push(list[i])
          }
        }
        return arr
      }
    }
  }
</script>
