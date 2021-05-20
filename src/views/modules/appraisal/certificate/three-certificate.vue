<template>
  <el-dialog title="三折页证书" :close-on-click-modal="false" :visible.sync="visible" width='100%'>
    <div class="certificate_wrap">
      <div class="certificate_box flex_box">
        <div class="certificate_item flex_item">
          <div class="item_header">
            <h3 class="title">艺术品基本信息</h3>
            <p class="intro">Basic Information Of Works Art</p>
          </div>
          <div class="certificate_info">
            <show-info-item title="艺术品名称" :value="certificate.name" />
            <show-info-item title="艺术品尺寸" :value="certificate.size" />
            <show-info-item title="艺术品重量" :value="certificate.weight" />
            <show-info-item title="艺术品材质" :value="certificate.mainMaterial" />
            <div class="sub_item flex_box">
              <div class="sub_side">
                <p class="sub_title">主体材质</p>
                <p class="sub_intro">Main Meterial</p>
              </div>
              <p class="sub_value flex_item">{{certificate.mainMaterial}}</p>
            </div>
            <div class="sub_item flex_box">
              <div class="sub_side">
                <p class="sub_title">副材质</p>
                <p class="sub_intro">Sub Meterial</p>
              </div>
              <p class="sub_value flex_item">{{certificate.subMaterial}}</p>
            </div>
            <show-info-item title="创作年代" :value="certificate.years" />
            <show-info-item title="鉴定托底价" :value="certificate.pawnPrice" />
            <show-info-item title="其他" :value="certificate.other" />
          </div>
        </div>
        <div class="certificate_item flex_item">
          <div class="item_header">
            <h3 class="title">证书编号</h3>
            <p class="intro">Certificate Number</p>
          </div>
          <div class="item_code">
            <img class="code" :src="showURL + certificate.codeImg" />
            <p class="code_number">{{certificate.code}}</p>
          </div>
          <show-picture :showData="pictureData" />
          <p class="company">江苏标普艺术品研究院</p>
        </div>
        <div class="certificate_item analysis_box">
          <div class="item_header">
            <h3 class="title">艺术品流通分析</h3>
            <p class="intro">Artwork Circulation Analysis</p>
          </div>
          <div class="show_analysis">
            <show-star title="市场流通性" english="Market liquidity" :starNum="Number(certificate.marketLiquidity)" />
            <show-star title="价值稳定性" english="Value stability" :starNum="Number(certificate.valueStability)" />
            <show-star title="材质易损性" english="Material Vulnerability" :starNum="Number(certificate.materialVulnerability)" />
          </div>
          <div class="show_attention">
            <div class="info_item flex_box">
              <span class="name flex_item">
                <p class="title">艺术品维护保养注意事项</p>
                <p class="intro">Matters needing attention in the maintenance of artworks</p>
              </span>
            </div>
            <div class="attention_box flex_box">
              <show-attention v-for="(item, index) in attentionBox" :key="index" :showObject="item" />
            </div>
          </div>
          <div class="show_attention">
            <div class="info_item flex_box">
              <span class="name flex_item">
                <p class="title">肉眼可见缺陷描述</p>
                <p class="intro">Visible defect Description with Naked Eye</p>
              </span>
            </div>
            <p class="tip">包括影响牢度的裂、磕、碰等</p>
            <p class="description">{{certificate.defectDescription}}</p>
          </div>
        </div>
      </div>
    </div>
  </el-dialog>
</template>
<script>
  import showInfoItem from './components/showInfoItem'
  import showPicture from './components/showPicture'
  import showStar from './components/showStar'
  import showAttention from './components/showAttention'
  export default {
    name: 'Home',
    components: {
      showInfoItem,
      showPicture,
      showStar,
      showAttention
    },
    data() {
      return {
        visible: false,
        showURL: '',
        pictureData: {
          mainPicture: 'http://i1.sinaimg.cn/IT/2010/0419/201041993740.jpg',
          subPictures: [
            'http://i1.sinaimg.cn/IT/2010/0419/201041993740.jpg',
            'http://i1.sinaimg.cn/IT/2010/0419/201041993740.jpg',
            'http://i1.sinaimg.cn/IT/2010/0419/201041993740.jpg'
          ]
        },
        attentionBox: [{
            title: '防潮',
            english: 'Moi-sture proof',
            isSelect: false,
            remark: ''
          },
          {
            title: '防热',
            english: 'Moi-sture proof',
            isSelect: false,
            remark: ''
          },
          {
            title: '防震',
            english: 'Moi-sture proof',
            isSelect: false,
            remark: ''
          },
          {
            title: '防碎',
            english: 'Moi-sture proof',
            isSelect: false,
            remark: ''
          },
          {
            title: '防霉',
            english: 'Moi-sture proof',
            isSelect: false,
            remark: ''
          },
          {
            title: '防紫外线',
            english: 'Moi-sture proof',
            isSelect: false,
            remark: ''
          },
          {
            title: '防虫',
            english: 'Moi-sture proof',
            isSelect: false,
            remark: ''
          }
        ],
        certificate: {}
      }
    },
    methods: {
      init(id) {
        this.visible = true
        this.showURL = window.SITE_CONFIG.baseUrl + '/sys/file/show/'
        this.$http({
          url: this.$http.adornUrl('/appraisal/getCertificate'),
          method: 'get',
          params: this.$http.adornParams({
            appraisalId: id
          })
        }).then(({
          data
        }) => {
          if (data && data.code === 0) {
            console.log(data)
            this.certificate = data.data
            // 防潮
            this.attentionBox[0].remark = data.data.dampproofRemark
            // 防热
            this.attentionBox[1].remark = data.data.antipyreticRemark
            // 防震
            this.attentionBox[2].remark = data.data.shockproofRemark
            // 防碎
            this.attentionBox[3].remark = data.data.shatterproofRemark
            // 防霉
            this.attentionBox[4].remark = data.data.mouldproofRemark
            // 防紫外线
            this.attentionBox[5].remark = data.data.ultravioletproofRemark
            // 防虫
            this.attentionBox[6].remark = data.data.pestcontrolRemark
            // 设置单选值
            // 防潮
            if (data.data.isDampproof === '1') {
              this.attentionBox[0].isSelect = true
            }
            // 防热
            if (data.data.isAntipyretic === '1') {
              this.attentionBox[1].isSelect = true
            }
            // 防震
            if (data.data.isShockproof === '1') {
              this.attentionBox[2].isSelect = true
            }
            // 防碎
            if (data.data.isShatterproof === '1') {
              this.attentionBox[3].isSelect = true
            }
            // 防霉
            if (data.data.isMouldproof === '1') {
              this.attentionBox[4].isSelect = true
            }
            // 防紫外线
            if (data.data.isUltravioletproof === '1') {
              this.attentionBox[5].isSelect = true
            }
            // 防虫
            if (data.data.isPestcontrol === '1') {
              this.attentionBox[6].isSelect = true
            }
          } else {
            this.certificate = {}
          }
        })
      }
    }
  }
</script>
<style lang="css">
  @import './index.css';
</style>
