<template>
  <div class="mod-user">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.name" placeholder="姓名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.phone" placeholder="手机号" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">{{ $t('m.query') }}</el-button>
      </el-form-item>
    </el-form>

    <el-table :data="dataList" border stripe v-loading="dataListLoading" @selection-change="selectionChangeHandle"
              style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <el-table-column prop="id" header-align="center" align="center" width="80" label="ID"
                       v-if="false"></el-table-column>
      <el-table-column prop="classifyName" header-align="center" align="center" label="鉴定类型"></el-table-column>
      <el-table-column prop="method" header-align="center" align="center" label="鉴定方式" width="100">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.method === '6'" style="color: #3333CC;">邮寄鉴定</el-tag>
          <el-tag v-else-if="scope.row.method === '3'" style="color: #33CC00;">在线鉴定</el-tag>
          <el-tag v-else-if="scope.row.method === '9'" style="color: #FF3300;">邮寄复检</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="state" header-align="center" align="center" label="鉴定状态" width="120">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.state === '0'" style="color: #3333CC;">待鉴定</el-tag>
          <el-tag v-else-if="scope.row.state === '1' || scope.row.state === '5' " style="color: #CCCC00;">已分配专家</el-tag>
          <el-tag v-else-if="scope.row.state === '2'" style="color: #33CC00;">鉴定完成</el-tag>
          <el-tag v-else-if="scope.row.state === '3'" style="color: #FF3300;">无法鉴定</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="number" header-align="center" align="center" label="鉴定编号" width="200">
        <template slot-scope="scope">
          <a style="cursor: pointer;"
             @click="showDetail(scope.row.id, scope.row.classify, scope.row.method)">{{ scope.row.number }}</a>
        </template>
      </el-table-column>
      <el-table-column prop="goods_code" header-align="center" align="center" label="商品编号" width="200"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="images" header-align="center" align="center" label="图片" width="400">
        <template slot-scope="scope">
          <span v-for="(item, index) in scope.row.cover_photo.split(',')" :key="index">
            <el-popover v-if="item != ''" placement="left" trigger="hover" width="500">
              <img :src="baseUrl + item" width="100%"/>
              <img slot="reference" :src="baseUrl + item" :alt="baseUrl + item"
                   style="max-height: 40px;max-width: 40px; padding: 5px"/>
            </el-popover>
          </span>
        </template>
      </el-table-column>
      <el-table-column prop="USER" header-align="center" align="center" label="提交人" width="150"></el-table-column>
      <el-table-column prop="create_time" header-align="center" align="center" label="提交时间"
                       width="180"></el-table-column>
      <el-table-column prop="expert" header-align="center" align="center" label="最终选择专家" width="180"></el-table-column>
      <el-table-column prop="isShow" header-align="center" align="center" label="显示方式">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.is_show === '1'" type="info">显示</el-tag>
          <el-tag v-else-if="scope.row.is_show === '0'" type="warning">不显示</el-tag>
        </template>
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="450" :label="$t('m.operation')">
        <template slot-scope="scope">
          <el-button type="text" v-if="scope.row.method === '6' || scope.row.method === '9'" size="small"
                     @click="uploadVideo(scope.row.id)">上传视频
          </el-button>
          <el-button type="text" v-if="scope.row.state === '0'" size="small" @click="chooseExperts(scope.row.id)">选择专家
          </el-button>
          <el-button type="text" v-if="scope.row.state === '1' || scope.row.state === '5' " size="small"
                     @click="chooseResult(scope.row.id, scope.row.state)">选择结果
          </el-button>
          <el-button type="text" v-if="scope.row.state === '2' || scope.row.state === '3'" size="small"
                     @click="viewResult(scope.row.id)">查看鉴定结果
          </el-button>
          <!--           || scope.row.two_certificate_code != '' || scope.row.two_certificate_code == null)-->
          <el-button
            type="text"
            v-if="scope.row.state === '2' && scope.row.method == '6' && (scope.row.certificate_code == '' || scope.row.certificate_code == null)"
            size="small"
            @click="createZS(scope.row.id)"
          >
            生成证书
          </el-button>
          <el-button
            type="text"
            v-if="scope.row.state === '2' && scope.row.method == '6' && (scope.row.certificate_code != '' && scope.row.certificate_code != null) && scope.row.threePdfState != 4"
            size="small"
            @click="reCreateZs(scope.row.id)"
          >
            重新生成证书
          </el-button>

          <!--          v-if="scope.row.state === '2' && (scope.row.certificate_code != '' && scope.row.certificate_code != null)"-->
          <!--          <el-button-->
          <!--            type="text"-->
          <!--            v-if="scope.row.threeFFileId != null && scope.row.threeFFileId != ''"-->
          <!--            size="small"-->
          <!--            @click="viewZs(scope.row.threeFFileId)"-->
          <!--          >-->
          <!--            查看三折证书反面-->
          <!--          </el-button>-->

          <el-button
            type="text"
            v-if="scope.row.threePdfState == 4 && scope.row.threeFFileId != null && scope.row.threeFFileId != ''"
            size="small"
            @click="viewPdf('F3'+scope.row.threeFFileId,scope.row.certificate_code)"
          >
            查看三折证书反面
          </el-button>

          <el-button
            type="text"
            v-if="scope.row.twoPdfState == 4 && scope.row.twoFFileId != null && scope.row.twoFFileId != ''"
            size="small"
            @click="viewPdf('F2'+scope.row.twoFFileId,scope.row.certificate_code)"
          >
            查看二折证书反面
          </el-button>

          <!--          <el-button-->
          <!--            type="text"-->
          <!--            v-if="scope.row.twoFFileId != null && scope.row.twoFFileId != ''"-->
          <!--            size="small"-->
          <!--            @click="viewZs(scope.row.twoFFileId)"-->
          <!--          >-->
          <!--            查看二折证书反面-->
          <!--          </el-button>-->
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper"
    ></el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <experts-tree v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></experts-tree>
    <result v-if="chooseResultVisible" ref="chooseResult" @refreshDataList="getDataList"></result>
    <upload-video v-if="uploadVideoVisible" ref="uploadVideo" @refreshDataList="getDataList"></upload-video>
    <create-zs v-if="zsVisible" ref="createZs" @refreshDataList="getDataList"></create-zs>
    <viewResult ref="viewResult" @refreshDataList="getDataList"></viewResult>
    <calligraphy ref="calligraphy"></calligraphy>
    <diamonds ref="diamonds"></diamonds>
    <gemstone ref="gemstone"></gemstone>
    <jadeite ref="jadeite"></jadeite>
    <luxuries ref="luxuries"></luxuries>
    <metal ref="metal"></metal>
    <nephrite ref="nephrite"></nephrite>
    <porcelain ref="porcelain"></porcelain>
    <painting ref="painting"></painting>
    <wenwan ref="wenwan"></wenwan>
    <other ref="other"></other>
    <watch ref="watch"></watch>
    <ThreeCertificate ref="threeCertificate"></ThreeCertificate>
    <two-certificate ref="twoCertificate"></two-certificate>
    <mailIdentification ref="mailIdentification"></mailIdentification>
  </div>
</template>
<script>
import ExpertsTree from './expertsTree'
import result from './result'
import UploadVideo from './uploadVideo'
import CreateZs from './create-zs'
// 书法详情页
import calligraphy from './detail/calligraphy'
// 钻石详情页
import diamonds from './detail/diamonds'
// 彩色宝石详情页
import gemstone from './detail/gemstone'
// 翡翠玉石详情页
import jadeite from './detail/jadeite'
// 奢侈品详情页
import luxuries from './detail/luxuries'
// 贵金属详情页
import metal from './detail/metal'
// 和田玉详情页
import nephrite from './detail/nephrite'
// 瓷器详情页
import porcelain from './detail/porcelain'
// 绘画详情页
import painting from './detail/paintint'
// 文玩杂项详情页
import wenwan from './detail/wenwan'
// 其他详情页
import other from './detail/other'
// 手表详情页
import watch from './detail/watch'
// 查看鉴定结果页面
import viewResult from './viewResult'
// 三折页证书
import ThreeCertificate from './certificate/three-certificate'
// 二折页证书
import TwoCertificate from './certificate/two-certificate'
// 邮寄鉴定页面
import mailIdentification from './detail/mailIdentification'

export default {
  data() {
    return {
      dataForm: {
        name: '',
        phone: ''
      },
      baseUrl: '',
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
      chooseResultVisible: false,
      uploadVideoVisible: false,
      zsVisible: false
    }
  },
  components: {
    ExpertsTree,
    result,
    UploadVideo,
    CreateZs,
    calligraphy,
    diamonds,
    gemstone,
    jadeite,
    luxuries,
    metal,
    nephrite,
    porcelain,
    painting,
    wenwan,
    other,
    watch,
    viewResult,
    ThreeCertificate,
    TwoCertificate,
    mailIdentification
  },
  activated() {
    this.getDataList()
    this.baseUrl = window.SITE_CONFIG.baseUrl + '/sys/file/show/'
  },
  methods: {
    viewPdf(fileId, certificateCode) {
      window.open(this.$http.adornUrl(`/sys/file/viewPDF/${certificateCode + fileId}`));
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/appraisal/list'),
        method: 'get',
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          name: this.dataForm.name,
          phone: this.dataForm.phone
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list
          this.totalPage = data.page.totalCount
        } else {
          this.dataList = []
          this.totalPage = 0
        }
        this.dataListLoading = false
      })
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val
      this.pageIndex = 1
      this.getDataList()
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val
      this.getDataList()
    },
    // 多选
    selectionChangeHandle(val) {
      this.dataListSelections = val
    },
    // 删除
    deleteHandle(id) {
    },
    chooseExperts(id) {
      this.addOrUpdateVisible = true
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id)
      })
    },
    chooseResult(id, state) {
      this.chooseResultVisible = true
      this.$nextTick(() => {
        this.$refs.chooseResult.init(id, state)
      })
    },
    uploadVideo(id) {
      this.uploadVideoVisible = true
      this.$nextTick(() => {
        this.$refs.uploadVideo.init(id)
      })
    },
    showDetail(id, classify, method) {
      if (method === '3') {
        if (classify === '01') {
          this.$nextTick(() => {
            this.$refs.diamonds.init(id)
          })
        } else if (classify === '02') {
          this.$nextTick(() => {
            this.$refs.gemstone.init(id)
          })
        } else if (classify === '03') {
          this.$nextTick(() => {
            this.$refs.watch.init(id)
          })
        } else if (classify === '04') {
          this.$nextTick(() => {
            this.$refs.luxuries.init(id)
          })
        } else if (classify === '05') {
          this.$nextTick(() => {
            this.$refs.jadeite.init(id)
          })
        } else if (classify === '06') {
          this.$nextTick(() => {
            this.$refs.nephrite.init(id)
          })
        } else if (classify === '07') {
          this.$nextTick(() => {
            this.$refs.metal.init(id)
          })
        } else if (classify === '09') {
          this.$nextTick(() => {
            this.$refs.calligraphy.init(id)
          })
        } else if (classify === '08') {
          this.$nextTick(() => {
            this.$refs.porcelain.init(id)
          })
        } else if (classify === '10') {
          this.$nextTick(() => {
            this.$refs.painting.init(id)
          })
        } else if (classify === '11') {
          this.$nextTick(() => {
            this.$refs.wenwan.init(id)
          })
        } else if (classify === '12') {
          this.$nextTick(() => {
            this.$refs.other.init(id)
          })
        }
      } else {
        this.$nextTick(() => {
          this.$refs.mailIdentification.init(id)
        })
      }
    },
    createZS(id) {
      this.zsVisible = true
      this.$nextTick(() => {
        this.$refs.createZs.init(id)
      })
    },
    reCreateZs(id) {
      let _this = this;
      this.$http({
        url: this.$http.adornUrl('/appraisal/certificate/reCreateZs'),
        method: 'get',
        params: this.$http.adornParams({
          id: id
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          _this.getDataList()
        } else {
          _this.$message.error('生成证书失败，请确认证书模板文件是否存在！');
        }
      })
    },
    viewResult(id) {
      this.$nextTick(() => {
        this.$refs.viewResult.init(id)
      })
    },
    viewZs(zsbh) {
      this.$http({
        url: this.$http.adornUrl(`/sys/file/url/${zsbh}`),
        method: 'get',
        params: this.$http.adornParams()
      }).then(({data}) => {
        if (data != null) {
          window.open('https://view.officeapps.live.com/op/view.aspx?src=https://www.paidangwang.net/pawn' + data, '证书查看')
        } else {
          this.$message.error('找不到证书文件，请联系管理员！')
        }
      })
    }
    // createTwoFoldFrontHandler(appraisalCode) {
    //   this.$http({
    //     url: this.$http.adornUrl('/appraisal/createTwoFoldFront/' + appraisalCode),
    //     method: 'get'
    //   }).then(({data}) => {
    //     if (data && data.code === 0) {
    //       console.log(data)
    //     }
    //   })
    //   console.log(appraisalCode)
    // }
  }
}
</script>

