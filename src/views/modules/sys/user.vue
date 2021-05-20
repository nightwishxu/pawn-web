<template>
  <div class="mod-user">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.userName" :placeholder="$t('m.userName')" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">{{$t('m.query')}}</el-button>
        <el-button v-if="isAuth('sys:user:save')" type="primary" @click="addOrUpdateHandle()">{{$t('m.add')}}</el-button>
        <el-button v-if="isAuth('sys:user:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">{{$t('m.batchDelete')}}</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="exportExcel()">{{$t('m.export')}}</el-button>
      </el-form-item>
    </el-form>

    <el-table :data="dataList" border stripe v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
      <el-table-column prop="userId" header-align="center" align="center" width="80" label="ID"></el-table-column>
      <el-table-column prop="username" header-align="center" align="center" :label="$t('m.userName')"></el-table-column>
      <el-table-column prop="email" header-align="center" align="center" :label="$t('m.email')"></el-table-column>
      <el-table-column prop="mobile" header-align="center" align="center" :label="$t('m.phone')"></el-table-column>
      <el-table-column prop="status" header-align="center" align="center" :label="$t('m.status')">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status === 0" size="small" type="danger">{{$t('m.prohibit')}}</el-tag>
          <el-tag v-else size="small">{{$t('m.normal')}}</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="createTime" header-align="center" align="center" width="180" :label="$t('m.creatTime')"></el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" :label="$t('m.operation')">
        <template slot-scope="scope">
          <el-button v-if="isAuth('sys:user:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.userId)">{{$t('m.edit')}}</el-button>
          <el-button v-if="isAuth('sys:user:delete')" type="text" size="small" @click="deleteHandle(scope.row.userId)">{{$t('m.delete')}}</el-button>
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
  import AddOrUpdate from './user-add-or-update'
  export default {
    data() {
      return {
        dataForm: {
          userName: ''
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
          url: this.$http.adornUrl('/sys/user/list'),
          method: 'get',
          params: this.$http.adornParams({
            page: this.pageIndex,
            limit: this.pageSize,
            username: this.dataForm.userName
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
      // 新增 / 修改
      addOrUpdateHandle(id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle(id) {
        var userIds = id ? [id] : this.dataListSelections.map(item => {
          return item.userId
        })
        this.$confirm(this.$t('dict.delete'), this.$t('prompt.prompt'), {
          confirmButtonText: this.$t('prompt.yes'),
          cancelButtonText: this.$t('m.cancel'),
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/sys/user/delete'),
            method: 'post',
            data: this.$http.adornData(userIds, false)
          }).then(({
            data
          }) => {
            if (data && data.code === 0) {
              this.$message({
                message: this.$t('prompt.success'),
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }).catch(() => {})
      },
      exportExcel: function() {
        this.$http({
          url: this.$http.adornUrl(`/sys/user/export`),
          method: "get",
          responseType: 'blob', // 服务器返回的数据类型
          params: this.$http.adornParams({
            page: this.pageIndex,
            limit: this.pageSize,
            username: this.dataForm.userName
          })
        }).then(function(res) {
          // 此处有个坑。这里用content保存文件流，最初是content=res，但下载的test.xls里的内容如下图1， // 检查了下才发现，后端对文件流做了一层封装，所以将content指向res.data即可
          // 另外，流的转储属于浅拷贝，所以此处的content转储仅仅是便于理解，并没有实际作用=_=
          const content = res.data
          const blob = new Blob([content])
          // 构造一个blob对象来处理数据
          const fileName = '员工信息.xlsx' // 导出文件名 // 对于<a>标签，只有 Firefox 和 Chrome（内核） 支持 download 属性
          // IE10以上支持blob但是依然不支持download
          if ('download' in document.createElement('a')) { // 支持a标签download的浏览器
            const link = document.createElement('a')
            // 创建a标签
            link.download = fileName // a标签添加属性
            link.style.display = 'none'
            link.href = URL.createObjectURL(blob)
            document.body.appendChild(link)
            link.click() // 执行下载
            URL.revokeObjectURL(link.href) // 释放url
            document.body.removeChild(link) // 释放标签
          } else {
            // 其他浏览器
            navigator.msSaveBlob(blob, fileName)
          }
        }).catch((error) => {
          console.log(error)
        })
      }
    }
  }
</script>
