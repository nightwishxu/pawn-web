<template>
  <el-dialog
    :title="addType == 1 ? '新增' : '修改'"
    :visible.sync="open"
    width="60%"
    :before-close="closeHandler">
    <el-form
      ref="rotationForm"
      :model="rotationForm"
      :rules="rules"
      size="mini"
      label-width="100px"
    >
      <el-form-item
        label="轮播标题"
        prop="rotationTitle"
      >
        <el-input
          v-model="rotationForm.rotationTitle"
          placeholder="请输入轮播标题"
          :maxlength="200"
          show-word-limit
          clearable
          :style="{width: '100%'}"
        />
      </el-form-item>
      <el-form-item
        label="排序"
      >
        <el-input
          v-model="rotationForm.curOrders"
          placeholder="请输入排序"
          type="number"
          maxlength="5"
          :style="{width: '100%'}"
        />
      </el-form-item>
      <el-form-item
        label="封面类型"
      >
        <el-radio
          v-model="rotationForm.coverType"
          label="1"
        >
          本地上传
        </el-radio>
        <el-radio
          v-model="rotationForm.coverType"
          label="2"
        >
          URL
        </el-radio>
      </el-form-item>
      <el-form-item
        label="封面地址"
        v-if="rotationForm.coverType == 2"
      >
        <el-input
          v-model="rotationForm.coverValue"
          placeholder="请输入封面地址"
          :maxlength="300"
          show-word-limit
          clearable
          :style="{width: '100%'}"
        />
      </el-form-item>
      <el-form-item label="封面图" v-if="rotationForm.coverType == 1">
        <el-upload
          class="avatar-uploader el-upload--text"
          :action="actionURL"
          :show-file-list="false"
          :on-success="successHandler"
        >
          <img v-if="rotationForm.coverValue" :src="rotationForm.coverValue" class="avatar">
          <i v-else class="el-icon-plus avatar-uploader-icon"></i>
        </el-upload>
      </el-form-item>
      <el-form-item
        label="跳转状态"
      >
        <el-radio
          v-model="rotationForm.jumpType"
          label="1"
        >
          不跳转
        </el-radio>
        <!--        <el-radio-->
        <!--          v-model="rotationForm.jumpType"-->
        <!--          label="2"-->
        <!--        >-->
        <!--          跳转H5-->
        <!--        </el-radio>-->
        <el-radio
          v-model="rotationForm.jumpType"
          label="3"
        >
          跳转富文本
        </el-radio>
      </el-form-item>
      <el-form-item label="富文本" v-if="rotationForm.jumpType == 3">
        <tinymce v-model="rotationForm.jumpValue"></tinymce>
      </el-form-item>
      <!--      <el-form-item-->
      <!--        label="H5链接"-->
      <!--        v-if="rotationForm.jumpType == 2"-->
      <!--      >-->
      <!--        <el-input-->
      <!--          v-model="rotationForm.jumpValue"-->
      <!--          placeholder="请输入H5链接"-->
      <!--          :maxlength="300"-->
      <!--          show-word-limit-->
      <!--          clearable-->
      <!--          :style="{width: '100%'}"-->
      <!--        />-->
      <!--      </el-form-item>-->
    </el-form>
    <span slot="footer" class="dialog-footer">
    <el-button @click="closeHandler">取 消</el-button>
    <el-button type="primary" @click="subHandler">确 定</el-button>
  </span>
  </el-dialog>
</template>

<script>
import Tinymce from '../../../common/Tinymce'

export default {
  components: {Tinymce},
  props: {
    open: {
      required: true,
      type: Boolean
    },
    addType: {
      required: true,
      type: Number
    },
    rotationId: {
      required: false,
      type: String
    }
  },
  data() {
    return {
      rotationForm: {},
      rules: {
        rotationTitle: [
          {
            required: true,
            message: '请输入轮播标题',
            trigger: 'blur'
          }
        ]
      },
      actionURL: ''
    }
  },
  created() {
    this.actionURL = window.SITE_CONFIG.baseUrl + '/sys/file/upload'
    if (this.rotationId === "" || this.rotationId === null || typeof this.rotationId === "undefined") {
      this.rotationForm = {
        fileType: '1',
        jumpType: '1',
        coverValue: ''
      }
    } else {
      this.$http({
        url: this.$http.adornUrl('/app/mini/project/rotation/info/' + this.rotationId),
        method: 'get'
      }).then(({
                 data
               }) => {
        if (data && data.code === 0) {
          this.rotationForm = data.data;
        }
      })
    }
  },
  methods: {
    closeHandler() {
      this.$emit('update:open', false)
    },
    successHandler(res, file) {
      this.rotationForm.coverValue = window.SITE_CONFIG.baseUrl + '/sys/file/show/' + res.file_id;
    },
    subHandler() {
      this.$refs['rotationForm'].validate(valid => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl('/app/mini/project/rotation/save'),
            method: 'post',
            data: this.$http.adornData({
              id: this.rotationForm.id,
              jumpType: this.rotationForm.jumpType,
              jumpValue: this.rotationForm.jumpValue,
              coverType: this.rotationForm.coverType,
              coverValue: this.rotationForm.coverValue,
              rotationTitle: this.rotationForm.rotationTitle,
              curOrders: this.rotationForm.curOrders
            })
          }).then(({
                     data
                   }) => {
            if (data && data.code === 0) {
              this.$message.success("编辑成功");
                this.$emit("queryList");
                this.closeHandler();
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

<style>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}

.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}

.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>
