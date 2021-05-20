<template>
  <el-dialog
    :title="currentTitle"
    :visible.sync="addDialogOpen"
    width="60%"
    :before-close="closeHandler"
    append-to-body>
    <el-form ref="form" :model="form" :rules="rules" label-width="80px">
      <el-form-item label="流通方式" prop="mode">
        <el-input v-model="form.mode"></el-input>
      </el-form-item>
      <el-form-item label="流通价格" prop="price">
        <el-input v-model="form.price"></el-input>
      </el-form-item>
      <el-form-item label="流通对象" prop="target">
        <el-input v-model="form.target"></el-input>
      </el-form-item>
      <el-form-item label="流通时间" prop="dateStr">
        <el-date-picker
          v-model="form.date"
          align="right"
          type="date"
          placeholder="选择日期"
          :picker-options="pickerOptions">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="流通备注" prop="remark">
        <el-input type="textarea" v-model="form.remark"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="closeHandler">取 消</el-button>
      <el-button type="primary" @click="subHandler">保 存</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  props: {
    addDialogOpen: {
      required: true,
      type: Boolean
    },
    certificateId: {
      required: true,
      type: Number
    },
    appraisalCode: {
      required: true,
      type: String
    },
    recordId: {
      required: true,
      type: Number
    }
  },
  created() {
    this.currentTitle = "编辑流水-" + this.appraisalCode;
    if (this.recordId === -1) {
      this.form = {
        certificateId: this.certificateId
      };
    } else {
      this.$http({
        url: this.$http.adornUrl('/sys/certificateRecord/info/' + this.recordId),
        method: 'get'
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.form = data.data;
        }
      })
    }
  },
  data() {
    return {
      currentTitle: '',
      form: {},
      rules: {
        mode: [
          {required: true, message: '请输入流通方式', trigger: 'blur'},
          {max: 50, message: '长度最多 50 个字符', trigger: 'blur'}
        ],
        price: [
          {required: true, message: '请输入流通价格', trigger: 'blur'}
        ],
        target: [
          {required: true, message: '请输入流通对象', trigger: 'blur'},
          {max: 5, message: '长度最多 50 个字符', trigger: 'blur'}
        ],
        date: [
          {required: true, message: '请选择流通时间', trigger: 'blur'}
        ]
      },
      pickerOptions: {
        disabledDate(time) {
          return time.getTime() > Date.now();
        },
        shortcuts: [{
          text: '今天',
          onClick(picker) {
            picker.$emit('pick', new Date());
          }
        }, {
          text: '昨天',
          onClick(picker) {
            const date = new Date();
            date.setTime(date.getTime() - 3600 * 1000 * 24);
            picker.$emit('pick', date);
          }
        }, {
          text: '一周前',
          onClick(picker) {
            const date = new Date();
            date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
            picker.$emit('pick', date);
          }
        }]
      }
    }
  },
  methods: {
    closeHandler() {
      this.$emit('update:addDialogOpen', false)
    },
    subHandler() {
      this.$refs['form'].validate(valid => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl('/sys/certificateRecord/save'),
            method: 'post',
            data: this.$http.adornData(this.form)
          }).then(({
                     data
                   }) => {
            if (data.code === 0) {
              this.$emit('refreshList');
              this.$message.success("编辑成功")
              this.closeHandler();
            }
          })
        }
      })
    }
  }
}
</script>
