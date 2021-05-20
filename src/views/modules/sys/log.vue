<template>
  <div class="mod-log">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item><el-input v-model="dataForm.username" :placeholder="$t('m.userName')" clearable></el-input></el-form-item>
      <el-form-item><el-input v-model="dataForm.operation" :placeholder="$t('m.operation')" clearable></el-input></el-form-item>
      <el-form-item><el-input v-model="dataForm.method" :placeholder="$t('m.requestMethod')" clearable></el-input></el-form-item>
      <el-form-item><el-input v-model="dataForm.ip" :placeholder="$t('m.ipAddress')" clearable></el-input></el-form-item>
      <el-form-item>
        <el-button @click="showToggle()">高级检索</el-button><el-button @click="getDataList()">{{$t('m.query')}}</el-button>
      </el-form-item>
      <div v-show="isShow" :inline="true">
        <el-form-item><el-input v-model="dataForm.url" :placeholder="$t('m.URL')" clearable></el-input></el-form-item>
        <el-form-item><el-input v-model="dataForm.userAgent" :placeholder="$t('m.userAgent')" clearable></el-input></el-form-item>
        <el-form-item>
          <el-date-picker
            v-model="dataForm.createDate"
            type="datetimerange"
            :picker-options="pickerOptions"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            value-format="yyyy-MM-dd HH:mm:ss"
            @change="dateChange"
            align="right">
          </el-date-picker>
        </el-form-item>
      </div>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" style="width: 100%">
      <el-table-column prop="id" header-align="center" align="center" width="80" label="ID"></el-table-column>
      <el-table-column prop="username" header-align="center" align="center" :label="$t('m.userName')"></el-table-column>
      <el-table-column prop="operation" header-align="center" align="center" :label="$t('m.operation')"></el-table-column>
      <el-table-column prop="method" header-align="center" align="center" width="150" :show-overflow-tooltip="true" :label="$t('m.requestMethod')"></el-table-column>
      <el-table-column prop="params" header-align="center" align="center" width="150" :show-overflow-tooltip="true" :label="$t('m.requestParameters')"></el-table-column>
      <el-table-column prop="time" header-align="center" align="center" :label="$t('m.executionTime')+'(ms)'"></el-table-column>
      <el-table-column prop="ip" header-align="center" align="center" width="150" :label="$t('m.ipAddress')"></el-table-column>
      <!--<el-table-column prop="url" header-align="center" align="center" width="150" :show-overflow-tooltip="true" :label="$t('m.URL')"></el-table-column>-->
      <el-table-column prop="createDate" header-align="center" align="center" width="160" :label="$t('m.creatTime')"></el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="50" :label="$t('m.operation')">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="details(scope.row.id)">{{$t('m.details')}}</el-button>
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

    <log-detail v-if="detailVisible" ref="logDetail" @refreshDataList="getDataList"></log-detail>
  </div>
</template>

<script>
import logDetail from './log-detail'
export default {
  data() {
    return {
      dataForm: {
        username: '',
        operation: '',
        method: '',
        ip: '',
        url: '',
        userAgent: '',
        createDate: ''
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      selectionDataList: [],
      detailVisible: false,
      isShow: false,

      pickerOptions: {
        shortcuts: [{
          text: '最近一周',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 7)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近一个月',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 30)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近三个月',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 90)
            picker.$emit('pick', [start, end])
          }
        }]
      }
    }
  },
  components: {
    logDetail
  },
  created() {
    this.getDataList()
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/log/list'),
        method: 'post',
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          dataForm: JSON.stringify(this.dataForm)
        })
      }).then(({ data }) => {
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
    details(id) {
      this.detailVisible = true
      this.$nextTick(() => {
        this.$refs.logDetail.init(id)
      })
    },
    showToggle() {
      this.isShow = !this.isShow
    },
    dateChange(val) {
      this.form.createDate = val
    }
  }
}
</script>
