<template>
  <el-dialog :title="!dataForm.id ? $t('m.add') : $t('m.edit')" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item :label="$t('m.uniqueCode')" prop="typeId">
        <el-input v-model="dataForm.typeId" :placeholder="$t('m.uniqueCode')" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.dict')+$t('m.value')" prop="value">
        <el-input v-model="dataForm.value" :placeholder="$t('m.dict')+$t('m.value')"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.sort')" prop="sort">
        <el-input v-model="dataForm.sort" :placeholder="$t('m.sort')"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.describe')" prop="cn">
        <el-input v-model="dataForm.cn" :placeholder="$t('m.describe')"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.extend')+'1'" prop="ext1">
        <el-input v-model="dataForm.ext1" :placeholder="$t('m.extend')+'1'"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.extend')+'2'" prop="ext2">
        <el-input v-model="dataForm.ext2" :placeholder="$t('m.extend')+'2'"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.extend')+'3'" prop="ext3">
        <el-input v-model="dataForm.ext3" :placeholder="$t('m.extend')+'3'"></el-input>
      </el-form-item>
      <el-form-item :label="$t('m.status')" size="mini" prop="status">
        <el-radio-group v-model="dataForm.status">
          <el-radio label="1">{{$t('m.enable')}}</el-radio>
          <el-radio label="0">{{$t('m.prohibit')}}</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item :label="$t('m.remark')" prop="remark">
        <el-input v-model="dataForm.remark" :placeholder="$t('m.extend')"></el-input>
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
          id: 0,
          typeId: '',
          value: '',
          cn: '',
          sort: '',
          ext1: '',
          ext2: '',
          ext3: '',
          status: '1',
          remark: ''

        },
        dataRule: {
          value: [{
            required: true,
            message: this.$t('m.dict') + this.$t('m.value') + this.$t('prompt.cannotBeEmpty'),
            trigger: 'blur'
          }],
          cn: [{
            required: true,
            message: this.$t('m.describe') + this.$t('prompt.cannotBeEmpty'),
            trigger: 'blur'
          }]
        }
      }
    },
    methods: {
      init(typeId, id) {
        this.dataForm.id = id || 0
        this.dataForm.typeId = typeId
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/sys/dict/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({
              data
            }) => {
              if (data && data.code === 0) {
                this.dataForm.typeId = data.dict.typeId
                this.dataForm.value = data.dict.value
                this.dataForm.cn = data.dict.cn
                this.dataForm.sort = data.dict.sort
                this.dataForm.ext1 = data.dict.ext1
                this.dataForm.ext2 = data.dict.ext2
                this.dataForm.ext3 = data.dict.ext3
                this.dataForm.status = data.dict.status
                this.dataForm.remark = data.dict.remark
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit() {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/dict/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'typeId': this.dataForm.typeId,
                'value': this.dataForm.value,
                'sort': this.dataForm.sort,
                'cn': this.dataForm.cn,
                'ext1': this.dataForm.ext1,
                'ext2': this.dataForm.ext2,
                'ext3': this.dataForm.ext3,
                'status': this.dataForm.status,
                'remark': this.dataForm.remark
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
