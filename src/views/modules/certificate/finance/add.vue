<template>
  <el-dialog
    :title="currentTitle"
    :visible.sync="addDialogOpen"
    width="60%"
    :before-close="closeHandler"
    append-to-body>
    <el-form ref="form" :model="form" :rules="rules" label-width="80px">
      <el-form-item label="抵押金融" prop="mortgageFinancial">
        <el-input v-model="form.mortgageFinancial"></el-input>
      </el-form-item>
      <el-form-item label="典当机构" prop="mechanism">
        <el-input v-model="form.mechanism"></el-input>
      </el-form-item>
      <el-form-item label="日期" prop="dateStr">
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
    this.currentTitle = "编辑金融记录-" + this.appraisalCode;
    if (this.recordId === -1) {
      this.form = {
        certificateId: this.certificateId
      };
    } else {
      this.$http({
        url: this.$http.adornUrl('/sys/financialRecord/info/' + this.recordId),
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
        mortgageFinancial: [
          {required: true, message: '请输入抵押金融', trigger: 'blur'},
          {max: 50, message: '长度最多 50 个字符', trigger: 'blur'}
        ],
        mechanism: [
          {required: true, message: '请输入典当机构', trigger: 'blur'}
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
            url: this.$http.adornUrl('/sys/financialRecord/save'),
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
