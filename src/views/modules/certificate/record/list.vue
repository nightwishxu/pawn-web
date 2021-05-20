<template>
  <el-dialog
    :title="currentTitle"
    :visible.sync="certificateRecordOpen"

    width="80%"
    :before-close="handleClose">
    <el-form :inline="true">
      <el-form-item>
        <el-button type="primary" @click="addHandler">{{ $t('m.add') }}</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border stripe v-loading="dataListLoading"
              style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <el-table-column prop="mode" header-align="center" align="center" width="250" label="流通方式"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="price" header-align="center" align="center" width="150" label="流通价格"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="target" header-align="center" align="center" width="250" label="流通对象"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="date" header-align="center" align="center" width="200" label="流通时间"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="remark" header-align="center" align="center" label="备注"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column
        label="操作"
        align="center"
        width="120"
        fixed="right"
      >
        <template slot-scope="scope">
          <el-button size="mini" type="text"
                     @click="delHandler(scope.row.id)">删除
          </el-button>
          <el-button size="mini" type="text"
                     @click="updateHandler(scope.row)">修改
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="queryParams.page"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="queryParams.limit"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper"
    ></el-pagination>
    <span slot="footer" class="dialog-footer">
    <el-button @click="handleClose">关 闭</el-button>
  </span>

    <certificate-record-add :addDialogOpen.sync="addDialogOpen" v-if="addDialogOpen"
                            :certificateId.sync="certificateId"
                            :appraisalCode.sync="appraisalCode"
                            :recordId.sync="currentRecordId"
                            @refreshList="refreshList"></certificate-record-add>
  </el-dialog>
</template>

<script>
import CertificateRecordAdd from './add'

export default {
  components: {CertificateRecordAdd},
  props: {
    certificateRecordOpen: {
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
    }
  },
  data() {
    return {
      currentTitle: '',
      queryParams: {
        page: 1,
        limit: 10
      },
      dataList: [],
      totalPage: 0,
      dataListLoading: true,
      addDialogOpen: false,
      currentRecordId: -1
    }
  },
  created() {
    this.getList();
    this.currentTitle = '流水记录-' + this.appraisalCode;
  },
  mounted() {
  },
  methods: {
    refreshList() {
      this.queryParams.page = 1;
      this.getList();
    },
    updateHandler(row) {
      this.currentRecordId = row.id;
      this.addDialogOpen = true;
    },
    delHandler(id) {
      this.$http({
        url: this.$http.adornUrl('/sys/certificateRecord/delete/' + id),
        method: 'get'
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.$message.success("删除成功")
          this.refreshList();
        }
      })
    },
    addHandler() {
      this.currentRecordId = -1;
      this.addDialogOpen = true;
    },
    getList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl('/sys/certificateRecord/lists'),
        method: 'get',
        params: this.$http.adornParams({
          page: this.queryParams.page,
          limit: this.queryParams.size,
          certificateId: this.certificateId
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
    handleClose() {
      this.$emit('update:certificateRecordOpen', false)
    }
  }
}
</script>
