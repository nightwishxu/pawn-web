<template>
  <div>
    <div style="margin-bottom: 20px">
      <create-zs ref="createZs" :sup="supThis"/>
      <el-button @click="$refs.createZs.visible = true">添加证书</el-button>
    </div>
    <el-table :data="dataList" border stripe v-loading="dataListLoading"
              style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <!--      <el-table-column prop="code" header-align="center" align="center" width="250" label="证书code"-->
      <!--                       :show-overflow-tooltip="true"></el-table-column>-->
      <el-table-column prop="appraisalCode" header-align="center" align="center" width="200" label="鉴定编号"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="name" header-align="center" align="center" width="180" label="名称"
                       :show-overflow-tooltip="true"></el-table-column>

      <el-table-column prop="pawnPrice" header-align="center" align="center" width="150" label="典当价格"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="size" header-align="center" align="center" width="120" label="尺寸"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="weight" header-align="center" align="center" width="120" label="重量"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="mainMaterial" header-align="center" align="center" width="150" label="主材质"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="years" header-align="center" align="center" width="150" label="年代"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="marketLiquidity" header-align="center" align="center" width="150" label="时长流通性"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="valueStability" header-align="center" align="center" width="150" label="价值稳定性"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="materialVulnerability" header-align="center" align="center" width="150" label="材质易损性"
                       :show-overflow-tooltip="true"></el-table-column>

      <el-table-column prop="defect_description" header-align="center" align="center" width="150" label="缺陷描述"
                       :show-overflow-tooltip="true"></el-table-column>

      <el-table-column prop="createTime" header-align="center" align="center" width="200" label="创建时间"
                       :show-overflow-tooltip="true"></el-table-column>

      <el-table-column
        label="操作"
        align="center"
        width="470"
        fixed="right"
      >
        <template slot-scope="scope">
          <el-button size="mini" type="text"
                     @click="recordHandler(scope.row)">流通记录
          </el-button>
          <el-button size="mini" type="text"
                     @click="financeRecordHandler(scope.row)">金融记录
          </el-button>
          <el-button size="mini" type="text"
                     v-if="(scope.row.threeZFileId != '' &&  scope.row.threeZFileId != null ) || (scope.row.twoZFileId != '' &&  scope.row.twoZFileId != null )"
                     @click="createPositive(scope.row.appraisalCode)">重新生成正面证书
          </el-button>
          <el-button size="mini" type="text"
                     v-if="(scope.row.threeZFileId == '' ||  scope.row.threeZFileId == null ) || (scope.row.twoZFileId == '' ||  scope.row.twoZFileId == null )"
                     @click="createPositive(scope.row.appraisalCode)">生成正面证书
          </el-button>
          <!--          <el-button size="mini" type="text"-->
          <!--                     v-if="scope.row.twoZFileId != '' &&  scope.row.twoZFileId != null"-->
          <!--                     @click="viewZs(scope.row.twoZFileId)">查看二折页正面-->
          <!--          </el-button>-->
          <el-button size="mini" type="text"
                     v-if="scope.row.twoZFileId != '' &&  scope.row.twoZFileId != null"
                     @click="viewPdf('Z2' + scope.row.twoZFileId,scope.row.code)">查看二折页正面
          </el-button>
          <!--          <el-button size="mini" type="text"-->
          <!--                     v-if="scope.row.threeZFileId != '' &&  scope.row.threeZFileId != null"-->
          <!--                     @click="viewZs(scope.row.threeZFileId)">查看三折页正面-->
          <!--          </el-button>-->
          <el-button size="mini" type="text"
                     v-if="scope.row.threeZFileId != '' &&  scope.row.threeZFileId != null"
                     @click="viewPdf('Z3' + scope.row.threeZFileId,scope.row.code)">查看三折页正面
          </el-button>
          <el-button size="mini" type="text"
                     v-if="scope.row.twoFFileId != '' &&  scope.row.twoFFileId != null"
                     @click="viewPdf('F2' + scope.row.twoFFileId,scope.row.code)">查看二折页反面
          </el-button>
          <el-button size="mini" type="text"
                     v-if="scope.row.threeFFileId != '' &&  scope.row.threeFFileId != null"
                     @click="viewPdf('F3' + scope.row.threeFFileId,scope.row.code)">查看三折页反面
          </el-button>
          <el-button size="mini" type="text"
                     v-if="(scope.row.threeZFileId != '' &&  scope.row.threeZFileId != null ) || (scope.row.twoZFileId != '' &&  scope.row.twoZFileId != null )"
                     @click="zipDownLoad(scope.row)">打包下载
          </el-button>
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

    <certificate-record-list :certificateRecordOpen.sync="certificateRecordOpen"
                             :certificateId.sync="currentCertificate.id"
                             :appraisalCode.sync="currentCertificate.appraisalCode"
                             ref="recordListRef"
                             v-if="certificateRecordOpen">
    </certificate-record-list>

    <financial-record-list :financialRecordOpen.sync="financialRecordOpen"
                           :certificateId.sync="currentCertificate.id"
                           :appraisalCode.sync="currentCertificate.appraisalCode"
                           ref="recordListRef"
                           v-if="financialRecordOpen"></financial-record-list>
  </div>
</template>

<script>
import CertificateRecordList from './record/list'
import FinancialRecordList from './finance/list'
import createZs from './create-zs'

export default {
  components: {CertificateRecordList, FinancialRecordList, createZs},
  data() {
    return {
      pageSize: 10,
      supThis: this,
      pageIndex: 1,
      queryParams: {
        page: 1,
        limit: 10
      },
      dataList: [],
      totalPage: 0,
      dataListLoading: true,
      certificateRecordOpen: false,
      currentCertificate: {
        id: 0,
        appraisalCode: ''
      },
      financialRecordOpen: false
    }
  },
  created() {
    this.getList();
  },
  methods: {
    /**
     * 生成证书正面
     */
    createPositive(appraisalCode) {
      // /appraisal/certificate/createZ
      this.$http({
        url: this.$http.adornUrl('/appraisal/certificate/createZ'),
        method: 'get',
        params: this.$http.adornParams({
          appraisalCode: appraisalCode
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.$message.success('证书生成成功！')
          this.pageIndex = 1;
          this.getList();
        } else {
          this.$message.error('找不到证书文件，请联系管理员！')
        }
      })
    },
    recordHandler(row) {
      this.currentCertificate = {
        id: row.id,
        appraisalCode: row.appraisalCode
      }
      // this.$refs.recordListRef.initHandler();
      this.certificateRecordOpen = true;
    },
    zipDownLoad(row) {
      window.open(this.$http.adornUrl(`/sys/file/certificate/downLoadZip/${row.id}`));
      // this.$http({
      //   url: this.$http.adornUrl('/sys/file/certificate/downLoadZip/' + row.id),
      //   method: 'get',
      //   responseType: "blob"
      // }).then(({response}) => {
      //   let blob = new Blob([response.data], { type: "application/zip" });
      //   let url = window.URL.createObjectURL(blob);
      //   const link = document.createElement("a"); // 创建a标签
      //   link.href = url;
      //   link.download = row.name + ".zip"; // 重命名文件
      //   link.click();
      //   URL.revokeObjectURL(url); // 释放内存
      //   this.checkList = []
      // })
    },
    getList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl('/sys/certificate/page'),
        method: 'get',
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list
          this.totalPage = data.page.totalCount
          this.dataListLoading = false;
        } else {
          this.dataList = []
          this.totalPage = 0
        }
        this.dataListLoading = false
      })
    },
    sizeChangeHandle(val) {
      this.pageSize = val
      this.pageIndex = 1
      this.getList()
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val
      this.getList()
    },
    financeRecordHandler(row) {
      this.currentCertificate = {
        id: row.id,
        appraisalCode: row.appraisalCode
      }
      this.financialRecordOpen = true;
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
    },
    viewPdf(fileId, certificateCode) {
      window.open(this.$http.adornUrl(`/sys/file/viewPDF/${certificateCode + fileId}`));
    }
  }
}
</script>
