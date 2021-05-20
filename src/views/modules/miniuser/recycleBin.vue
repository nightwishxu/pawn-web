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
        <el-button @click="getDataList()">{{$t('m.query')}}</el-button>
      </el-form-item>
    </el-form>

    <el-table :data="dataList" border stripe v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <el-table-column prop="id" header-align="center" align="center" width="80" label="ID" v-if="false"></el-table-column>
      <el-table-column prop="name" header-align="center" align="center" label="姓名"></el-table-column>
      <el-table-column prop="phone" header-align="center" align="center" label="手机号"></el-table-column>
      <el-table-column prop="wxNick" header-align="center" align="center" label="微信昵称"></el-table-column>
      <el-table-column prop="miniOpenId" header-align="center" align="center" label="openId"></el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" :label="$t('m.operation')">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="recovery(scope.row.id)">恢复</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>
<script>
  import AddOrUpdate from './expert-add-or-update'
  export default {
    data() {
      return {
        dataForm: {
          name: '',
          phone: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated() {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList() {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/mini/user/recycleBin'),
          method: 'get',
          params: this.$http.adornParams({
            page: this.pageIndex,
            limit: this.pageSize,
            name: this.dataForm.name,
            phone: this.dataForm.phone
          })
        }).then(({
          data
        }) => {
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
      recovery(id) {
        this.$confirm("确定要恢复该人员吗？", this.$t('prompt.prompt'), {
          confirmButtonText: this.$t('prompt.yes'),
          cancelButtonText: this.$t('m.cancel'),
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl(`/mini/user/recovery/${id}`),
            method: 'get',
            data: this.$http.adornParams()
          }).then(({
            data
          }) => {
            if (data && data.code === 0) {
              this.$message({
                message: this.$t('prompt.success'),
                type: 'success'
              })
              this.getDataList()
            } else {
              this.$message.error(data.msg)
            }
          })
        }).catch(() => {})
      }
    }
  }
</script>
