<template>
  <el-dialog title="生成证书" :close-on-click-modal="false" :visible.sync="visible" @close="closed">
    <el-form ref="dataForm" :model="form" label-width="100px" :rules="dataRule">
      <el-form-item label="上传图片">
        <el-upload
          :multiple="false"
          :file-list="fileList"
          :show-file-list="true"
          class="editor-slide-upload"
          :action="actionURL"
          list-type="picture-card"
          :on-success="handleSuccess"
          :on-remove="handleRemove"
        >
          <el-button
            size="small"
            type="primary"
          >
            选择 图片
          </el-button>
        </el-upload>
      </el-form-item>
      <el-form-item label="鉴定类型" prop="classify">
        <el-select v-model="form.classify" style="width: 512px">
          <el-option v-for="item in typeList" :key="item.code" :value="item.code" :label="item.name"/>
        </el-select>
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
      <el-button type="primary" @click="dataFormSubmit(2)"
                 v-if="form.twoCertificateCode == '' || form.twoCertificateCode == null">生成证书</el-button>
      <!--      <el-button type="primary" @click="dataFormSubmit(2)" v-if="form.twoCertificateCode != '' && form.twoCertificateCode != null" >重新生成二折页</el-button>-->
      <!--      <el-button type="primary" @click="dataFormSubmit(3)" v-if="form.certificateCode == '' || form.certificateCode == null">生成三折页</el-button>-->
      <!--      <el-button type="primary" @click="dataFormSubmit(3)" v-if="form.certificateCode != '' && form.certificateCode != null">重新生成三折页</el-button>-->
    </span>
  </el-dialog>
</template>

<script>
export default {
  props: {
    sup: {
      type: Object,
      required: true
    }
  },
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
      fileList: [],
      fileIds: [],
      actionURL: '',
      typeList: [
        { code: '01', name: '钻石' },
        { code: '02', name: '彩色宝石' },
        { code: '03', name: '手表' },
        { code: '04', name: '奢侈品珠宝饰品' },
        { code: '05', name: '翡翠及饰品' },
        { code: '06', name: '和田玉及饰品' },
        { code: '07', name: '贵金属及贵金属饰品' },
        { code: '08', name: '瓷器' },
        { code: '09', name: '书法' },
        { code: '10', name: '绘画' },
        { code: '11', name: '文玩杂项' },
        { code: '12', name: '其他物品' }
      ],
      max: 4,
      visible: false,
      showURL: '',
      appraisalId: null,
      allImage: [],
      form: {
        classify: '',
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
        weight: [{required: true, message: '重量不能为空', trigger: 'blur'}],
        mainMaterial: [{required: true, message: '主材质不能为空', trigger: 'blur'}],
        subMaterial: [{required: true, message: '副材质不能为空', trigger: 'blur'}],
        years: [{required: true, message: '年份不能为空', trigger: 'blur'}],
        other: [{required: true, message: '其他不能为空', trigger: 'blur'}],
        marketLiquidity: [{required: true, validator: valiNumberPass2, trigger: 'blur'}],
        valueStability: [{required: true, validator: valiNumberPass2, trigger: 'blur'}],
        materialVulnerability: [{required: true, validator: valiNumberPass2, trigger: 'blur'}],
        pawnPrice: [{required: true, message: '典当价格不能为空', trigger: 'blur'}],
        classify: [{required: true, message: '鉴定类型不能为空', trigger: 'blur'}]
      }
    };
  },
  created() {
    this.actionURL = window.SITE_CONFIG.baseUrl + '/sys/file/upload'
  },
  methods: {
    handleSuccess(response, file) {
      this.fileIds.push(file.response.file_id)
      console.log(this.fileIds)
    },
    handleRemove(file) {
      const fileIds = []
      this.fileIds.forEach(f => {
        if (f !== file.response.file_id) {
          fileIds.push(f)
        }
      })
      this.fileIds = fileIds
      console.log(this.fileIds)
    },
    dataFormSubmit(createType) {
      let _this = this;
      this.form.images = this.fileIds.join(',');
      this.form.createType = createType;
      this.$http({
        url: this.$http.adornUrl('/appraisal/certificate/createNew'),
        method: 'get',
        params: this.$http.adornParams(this.form)
      }).then(({data}) => {
        if (data && data.code === 0) {
          _this.visible = false;
          _this.sup.getList()
        } else {
          this.$message.error('生成证书失败，请确认证书模板文件是否存在！');
        }
      });
    },
    closed() {
      this.allImage = [];
      this.form = {
        classify: '',
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
