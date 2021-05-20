<template>
  <el-dialog title="生成证书" :close-on-click-modal="false" :visible.sync="visible" @close="closed">
    <el-form ref="dataForm" :model="form" label-width="100px" :rules="dataRule">
      <el-form-item label="选择图片">
        <el-checkbox-group v-model="form.checkImages" :max="max">
          <el-checkbox v-for="(item, index) in allImage" :key="index" :label="item">
            <el-image style="width: 100px; height: 100px" :src="showURL + item"></el-image>
          </el-checkbox>
        </el-checkbox-group>
      </el-form-item>
      <el-form-item label="名称" prop="name">
        <el-input v-model="form.name"></el-input>
      </el-form-item>
      <el-form-item label="尺寸" prop="size">
        <el-input v-model="form.size"></el-input>
      </el-form-item>
      <el-form-item label="重量" prop="weight">
        <el-input v-model="form.weight"></el-input>
      </el-form-item>
      <el-form-item label="主材质" prop="mainMaterial">
        <el-input v-model="form.mainMaterial"></el-input>
      </el-form-item>
      <el-form-item label="副材质" prop="subMaterial">
        <el-input v-model="form.subMaterial"></el-input>
      </el-form-item>
      <el-form-item label="年代" prop="years">
        <el-input v-model="form.years"></el-input>
      </el-form-item>
      <el-form-item label="其他" prop="other">
        <el-input v-model="form.other"></el-input>
      </el-form-item>
      <el-form-item label="市场流通性" prop="marketLiquidity">
        <el-input v-model="form.marketLiquidity"></el-input>
      </el-form-item>
      <el-form-item label="价值稳定性" prop="valueStability">
        <el-input v-model="form.valueStability"></el-input>
      </el-form-item>
      <el-form-item label="材质易损性" prop="materialVulnerability">
        <el-input v-model="form.materialVulnerability"></el-input>
      </el-form-item>
      <el-form-item label="典当价格" prop="pawnPrice">
        <el-input v-model="form.pawnPrice"></el-input>
      </el-form-item>
      <el-form-item label="是否防潮">
        <el-switch v-model="form.isDampproof" active-color="#13ce66" inactive-color="#ff4949" active-value="1"
                   inactive-value="0"></el-switch>
      </el-form-item>
      <el-form-item label="防潮备注">
        <el-input v-model="form.dampproofRemark" placeholder="请输入防潮备注"></el-input>
      </el-form-item>
      <el-form-item label="是否防震">
        <el-switch v-model="form.isShockproof" active-color="#13ce66" inactive-color="#ff4949" active-value="1"
                   inactive-value="0"></el-switch>
      </el-form-item>
      <el-form-item label="防震备注">
        <el-input v-model="form.shockproofRemark" placeholder="请输入防震备注"></el-input>
      </el-form-item>
      <el-form-item label="是否防霉">
        <el-switch v-model="form.isMouldproof" active-color="#13ce66" inactive-color="#ff4949" active-value="1"
                   inactive-value="0"></el-switch>
      </el-form-item>
      <el-form-item label="防霉备注">
        <el-input v-model="form.mouldproofRemark" placeholder="请输入防霉备注"></el-input>
      </el-form-item>
      <el-form-item label="是否防虫">
        <el-switch v-model="form.isPestcontrol" active-color="#13ce66" inactive-color="#ff4949" active-value="1"
                   inactive-value="0"></el-switch>
      </el-form-item>
      <el-form-item label="防虫备注">
        <el-input v-model="form.pestcontrolRemark" placeholder="请输入防虫备注"></el-input>
      </el-form-item>
      <el-form-item label="是否防热">
        <el-switch v-model="form.isAntipyretic" active-color="#13ce66" inactive-color="#ff4949" active-value="1"
                   inactive-value="0"></el-switch>
      </el-form-item>
      <el-form-item label="防热备注">
        <el-input v-model="form.antipyreticRemark" placeholder="请输入防热备注"></el-input>
      </el-form-item>
      <el-form-item label="是否防碎">
        <el-switch v-model="form.isShatterproof" active-color="#13ce66" inactive-color="#ff4949" active-value="1"
                   inactive-value="0"></el-switch>
      </el-form-item>
      <el-form-item label="防碎备注">
        <el-input v-model="form.shatterproofRemark" placeholder="请输入防碎备注"></el-input>
      </el-form-item>
      <el-form-item label="是否防紫外线">
        <el-switch v-model="form.isUltravioletproof" active-color="#13ce66" inactive-color="#ff4949" active-value="1"
                   inactive-value="0"></el-switch>
      </el-form-item>
      <el-form-item label="防紫外线备注">
        <el-input v-model="form.ultravioletproofRemark" placeholder="请输入防紫外线备注"></el-input>
      </el-form-item>
      <el-form-item label="缺陷描述">
        <el-input v-model="form.defectDescription" placeholder="请输入缺陷描述"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">{{ $t('m.cancel') }}</el-button>
      <el-button type="primary" @click="dataFormSubmit(2)" v-if="form.twoCertificateCode == '' || form.twoCertificateCode == null" >生成证书</el-button>
<!--      <el-button type="primary" @click="dataFormSubmit(2)" v-if="form.twoCertificateCode != '' && form.twoCertificateCode != null" >重新生成二折页</el-button>-->
<!--      <el-button type="primary" @click="dataFormSubmit(3)" v-if="form.certificateCode == '' || form.certificateCode == null">生成三折页</el-button>-->
<!--      <el-button type="primary" @click="dataFormSubmit(3)" v-if="form.certificateCode != '' && form.certificateCode != null">重新生成三折页</el-button>-->
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
    var valiNumberPass2 = (rule, value, callback) => {
      // 正整数
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
      max: 4,
      visible: false,
      showURL: '',
      appraisalId: null,
      allImage: [],
      form: {
        appraisalId: '',
        checkImages: [],
        isDampproof: '1',
        dampproofRemark: '',
        isShockproof: '1',
        shockproofRemark: '',
        isMouldproof: '1',
        mouldproofRemark: '',
        isPestcontrol: '1',
        pestcontrolRemark: '',
        isAntipyretic: '1',
        antipyreticRemark: '',
        isShatterproof: '1',
        shatterproofRemark: '',
        isUltravioletproof: '1',
        ultravioletproofRemark: '',
        defectDescription: '',
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
      dataRule: {
        name: [{required: true, message: '名称不能为空', trigger: 'blur'}],
        size: [{required: true, message: '尺寸不能为空', trigger: 'blur'}],
        weight: [{required: true, validator: valiNumberPass1, trigger: 'blur'}],
        mainMaterial: [{required: true, message: '主材质不能为空', trigger: 'blur'}],
        subMaterial: [{required: true, message: '副材质不能为空', trigger: 'blur'}],
        years: [{required: true, message: '年份不能为空', trigger: 'blur'}],
        other: [{required: true, message: '年份不能为空', trigger: 'blur'}],
        marketLiquidity: [{required: true, validator: valiNumberPass2, trigger: 'blur'}],
        valueStability: [{required: true, validator: valiNumberPass2, trigger: 'blur'}],
        materialVulnerability: [{required: true, validator: valiNumberPass2, trigger: 'blur'}],
        pawnPrice: [{required: true, validator: valiNumberPass1, trigger: 'blur'}]
      }
    };
  },
  methods: {
    init(id) {
      this.visible = true;
      this.form.appraisalId = id;
      this.showURL = window.SITE_CONFIG.baseUrl + '/sys/file/show/';
      let _this = this;
      this.$http({
        url: this.$http.adornUrl('/appraisal/photo'),
        method: 'get',
        params: this.$http.adornParams({appraisalId: id})
      }).then(({data}) => {
        if (data && data.code === 0) {
          for (let photo of data.data) {
            _this.allImage.push(photo.id);
          }
        }
      });
      this.$http({
        url: this.$http.adornUrl('/appraisal/detail'),
        method: 'get',
        params: this.$http.adornParams({
          id: id
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          console.log("123", data)
          _this.form.name = data.data.name;
          _this.form.size = data.data.size;
          _this.form.weight = data.data.weight;
          _this.form.mainMaterial = data.data.mainMaterial;
          _this.form.subMaterial = data.data.subMaterial;
          _this.form.years = data.data.years;
          _this.form.other = data.data.other;
          _this.form.marketLiquidity = data.data.marketLiquidity;
          _this.form.valueStability = data.data.valueStability;
          _this.form.materialVulnerability = data.data.materialVulnerability;
          _this.form.pawnPrice = data.data.pawnPrice;
          _this.form.twoCertificateCode = data.data.twoCertificateCode;
          _this.form.certificateCode = data.data.certificateCode;
        }
      });
    },
    dataFormSubmit(createType) {
      let _this = this;
      this.form.images = this.form.checkImages.join(',');
      this.form.createType = createType;
      this.$http({
        url: this.$http.adornUrl('/appraisal/certificate/create'),
        method: 'get',
        params: this.$http.adornParams(this.form)
      }).then(({data}) => {
        if (data && data.code === 0) {
          _this.visible = false;
          _this.$emit('refreshDataList');
        } else {
          this.$message.error('生成证书失败，请确认证书模板文件是否存在！');
        }
      });
    },
    closed() {
      this.allImage = [];
      this.form = {
        appraisalId: '',
        checkImages: [],
        isDampproof: '1',
        dampproofRemark: '',
        isShockproof: '1',
        shockproofRemark: '',
        isMouldproof: '1',
        mouldproofRemark: '',
        isPestcontrol: '1',
        pestcontrolRemark: '',
        isAntipyretic: '1',
        antipyreticRemark: '',
        isShatterproof: '1',
        shatterproofRemark: '',
        isUltravioletproof: '1',
        ultravioletproofRemark: '',
        defectDescription: ''
      };
    }
  }
};
</script>
