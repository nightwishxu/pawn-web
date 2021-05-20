<template>
  <div class="upload-container">
    <el-link
      type="primary"
      @click=" dialogVisible=true"
    >
      图片上传
    </el-link>
    <el-dialog
      :visible.sync="dialogVisible"
      append-to-body
    >
      <!--      <el-upload-->
      <!--        :multiple="false"-->
      <!--        :file-list="fileList"-->
      <!--        :show-file-list="true"-->
      <!--        :on-remove="handleRemove"-->
      <!--        :on-success="handleSuccess"-->
      <!--        :before-upload="beforeUpload"-->
      <!--        class="editor-slide-upload"-->
      <!--        action="https://httpbin.org/post"-->
      <!--        list-type="picture-card"-->
      <!--      >-->
      <el-upload
        :multiple="false"
        :file-list="fileList"
        :show-file-list="true"
        class="editor-slide-upload"
        :action="actionURL"
        list-type="picture-card"
        :on-success="successHandler"
      >
        <el-button
          size="small"
          type="primary"
        >
          选择 图片
        </el-button>
      </el-upload>
      <el-button @click="dialogVisible = false">
        取消
      </el-button>
      <el-button
        type="primary"
        @click="handleSubmit"
      >
        上传
      </el-button>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: 'EditorSlideUpload',
  props: {
    color: {
      type: String,
      default: '#1890ff'
    },
    batchId: {
      required: true,
      type: String
    }
  },
  data() {
    return {
      dialogVisible: false,
      listObj: {},
      fileList: [],
      glaveFiles: [],
      actionURL: ''
    }
  },
  created() {
    this.actionURL = window.SITE_CONFIG.baseUrl + '/sys/file/upload'
  },
  methods: {
    checkAllSuccess() {
      return Object.keys(this.listObj).every(item => this.listObj[item].hasSuccess)
    },
    handleSubmit() {
      // const arr = Object.keys(this.listObj).map(v => this.listObj[v])
      if (!this.checkAllSuccess()) {
        this.$message('Please wait for all images to be uploaded successfully. If there is a network problem, please refresh the page and upload again!')
        return
      }
      this.$emit('successCBK', this.glaveFiles);
      this.listObj = {}
      this.fileList = []
      this.dialogVisible = false
    },
    handleSuccess(response, file) {
      const uid = file.uid
      const objKeyArr = Object.keys(this.listObj)
      for (let i = 0, len = objKeyArr.length; i < len; i++) {
        if (this.listObj[objKeyArr[i]].uid === uid) {
          this.listObj[objKeyArr[i]].url = response.files.file
          this.listObj[objKeyArr[i]].hasSuccess = true
          return
        }
      }
    },
    handleRemove(file) {
      const uid = file.uid
      const objKeyArr = Object.keys(this.listObj)
      for (let i = 0, len = objKeyArr.length; i < len; i++) {
        if (this.listObj[objKeyArr[i]].uid === uid) {
          delete this.listObj[objKeyArr[i]]
          return
        }
      }
    },
    beforeUpload(file) {
      const _self = this
      const _URL = window.URL || window.webkitURL
      const fileName = file.uid
      this.listObj[fileName] = {}
      return new Promise((resolve, reject) => {
        const img = new Image()
        img.src = _URL.createObjectURL(file)
        img.onload = function() {
          _self.listObj[fileName] = {hasSuccess: false, uid: file.uid, width: this.width, height: this.height}
        }
        resolve(true)
      })
    },
    successHandler(res, file) {
      this.glaveFiles[0] = window.SITE_CONFIG.baseUrl + '/sys/file/show/' + res.file_id;
    },
    requestUpload(param) {
      let uploadFormData = new FormData();
      uploadFormData.append("file", param.file);
      uploadFormData.append("upLoadType", "PROD_CONTENT");
      uploadFormData.append("batchId", this.batchId);
      // upLoadImg(uploadFormData).then(response => {
      //   if (response.status == 200) {
      //     this.glaveFiles[0] = response.data.data.filePath;
      //   }
      // });
    }
  }
}
</script>

<style lang="scss" scoped>
.editor-slide-upload {
  margin-bottom: 20px;

  /deep/ .el-upload--picture-card {
    width: 100%;
  }
}
</style>
