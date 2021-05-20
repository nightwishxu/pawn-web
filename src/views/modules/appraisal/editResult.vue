<template>
  <el-dialog title="鉴定结果" :close-on-click-modal="false" :visible.sync="visible" :append-to-body="true">
    <el-form :model="data" ref="dataForm"  label-width="100px" :rules="dataRule">
      <template v-if="data.state === '3'">
        <el-form-item label="鉴定结果">无法鉴定</el-form-item>
        <el-form-item label="理由"><el-input v-model="data.reason"></el-input></el-form-item>
      </template>
      <template v-if="data.state === '2'">
        <el-form-item label="名称" prop="name"><el-input v-model="data.name"></el-input></el-form-item>
        <el-form-item label="尺寸" prop="size"><el-input v-model="data.size"></el-input></el-form-item>
        <el-form-item label="重量" prop="weight"><el-input v-model="data.weight"></el-input></el-form-item>
        <el-form-item label="主材质" prop="mainMaterial"><el-input v-model="data.mainMaterial"></el-input></el-form-item>
        <el-form-item label="副材质" prop="subMaterial"><el-input v-model="data.subMaterial"></el-input></el-form-item>
        <el-form-item label="年代" prop="years"><el-input v-model="data.years"></el-input></el-form-item>
        <el-form-item label="其他" prop="other"><el-input v-model="data.other"></el-input></el-form-item>
        <el-form-item label="市场流通性" prop="marketLiquidity"><el-input v-model="data.marketLiquidity"></el-input></el-form-item>
        <el-form-item label="价值稳定性" prop="valueStability"><el-input v-model="data.valueStability"></el-input></el-form-item>
        <el-form-item label="材质易损性" prop="materialVulnerability"><el-input v-model="data.materialVulnerability"></el-input></el-form-item>
        <el-form-item label="典当价格" prop="pawnPrice"><el-input v-model="data.pawnPrice"></el-input></el-form-item>
      </template>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">{{ $t('m.cancel') }}</el-button>
      <el-button type="primary" @click="dataFormSubmit()">{{ $t('m.save') }}</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    var valiNumberPass1 = (rule, value, callback) => {
      // 包含小数的数字
      let reg = /^[+-]?(0|([1-9]\d*))(\.\d+)?$/g;
      if (value === '') {
        callback(new Error('请输入内容'));
      } else if (!reg.test(value)) {
        callback(new Error('请输入数字'));
      } else {
        callback();
      }
    };
    var valiNumberPass2 = (rule, value, callback) => { // 正整数
        let reg = /^[+]{0,1}(\d+)$/g;
        if (value === '') {
            callback(new Error('请输入内容'));
        } else if (!reg.test(value)) {
            callback(new Error('请输入0及0以上的整数'));
        } else {
            callback();
        }
    };
    return {
      visible: false,
      showURL: '',
      resultId: null,
      appraisalId: null,
      data: {
        name: '',
        size: '',
        weight: '',
        mainMaterial: '',
        subMaterial: '',
        years: '',
        other: '',
        marketLiquidity: '',
        valueStability: '',
        materialVulnerability: '',
        pawnPrice: ''
      },
      photosList: [],
      enclosureList: [],
      dataRule: {
        name: [
          {required: true, message: '名称不能为空', trigger: 'blur'}
        ],
        size: [
          {required: true, message: '尺寸不能为空', trigger: 'blur'}
        ],
        weight: [
          { required: true, validator: valiNumberPass1, trigger: "blur" }
        ],
        mainMaterial: [
          { required: true, message: '主材质不能为空', trigger: "blur" }
        ],
        subMaterial: [
          { required: true, message: '副材质不能为空', trigger: "blur" }
        ],
        years: [
          { required: true, message: '年份不能为空', trigger: "blur" }
        ],
        other: [
          { required: true, message: '年份不能为空', trigger: "blur" }
        ],
        marketLiquidity: [
          { required: true, validator: valiNumberPass2, trigger: "blur" }
        ],
        valueStability: [
          { required: true, validator: valiNumberPass2, trigger: "blur" }
        ],
        materialVulnerability: [
          { required: true, validator: valiNumberPass2, trigger: "blur" }
        ],
        pawnPrice: [
          { required: true, validator: valiNumberPass1, trigger: "blur" }
        ]
      }
    };
  },
  methods: {
    init(id, appraisalId) {
      this.resultId = id;
      this.appraisalId = appraisalId;
      this.visible = true;
      let _this = this;
      this.$http({
        url: this.$http.adornUrl('/experts/result'),
        method: 'get',
        params: this.$http.adornParams({
          id: id
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          _this.data = data.data;
        }
      });
    },
    dataFormSubmit() {
      this.$refs['dataForm'].validate(valid => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/appraisal/changeAndChoose/${this.appraisalId}/${this.resultId}`),
            method: 'post',
            data: this.$http.adornData(this.data)
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: this.$t('prompt.success'),
                type: 'success',
                onClose: () => {
                  this.visible = false;
                  this.$emit('closeAndRefresh');
                }
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
      })
    }
  }
};
</script>
