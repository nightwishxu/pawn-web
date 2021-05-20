<template>
  <div>
    <el-form :inline="true">
      <el-form-item>
        <el-button type="primary" icon="el-icon-plus" @click="addHandler">新增</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border stripe v-loading="dataListLoading"
              style="width: 100%;">
      <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>

      <el-table-column prop="id" header-align="center" align="center" width="80" label="ID"
                       v-if="false"></el-table-column>
      <el-table-column
        label="封面"
        width="100"
        height="100"
        align="center"
        fixed="left"
      >
        <template slot-scope="scope">
          <img
            :src="scope.row.coverValue"
            lazy
            width="36"
            height="36"
            alt
          >
        </template>
      </el-table-column>
      <el-table-column prop="rotationTitle" header-align="center" align="center" label="标题"
                       width="300"></el-table-column>
      <el-table-column prop="method" header-align="center" align="center" label="跳转方式" width="200">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.jumpType == '1'">不跳转</el-tag>
          <el-tag v-if="scope.row.jumpType == '2'">跳转H5</el-tag>
          <el-tag v-if="scope.row.jumpType == '3'">跳转富文本</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="curOrders" header-align="center" align="center" label="排序" width="100"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column header-align="center" align="center" width="450" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="updateHandler(scope.row.id)">
            修改
          </el-button>
          <el-button type="text" size="small" @click="deleteHandler(scope.row.id)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <rotation-add :open.sync="rotationAddDialog.open" v-if="rotationAddDialog.open"
                  :addType="rotationAddDialog.addType"
                  :rotation-id="rotationAddDialog.rotationId"
                  @queryList="queryHandler"
    >

    </rotation-add>
  </div>

</template>

<script>
import RotationAdd from './rotation/add'

export default {
  components: {
    RotationAdd
  },
  data() {
    return {
      rotationAddDialog: {
        open: false,
        addType: 1,
        rotationId: ''
      },
      rotationList: [],
      addOrUpdateOpen: false,
      queryParams: {
        pages: 1,
        sizes: 10
      },
      dataListLoading: true,
      dataList: [],
      totalCount: 0
    }
  },
  created() {
    this.getList();
  },
  methods: {
    addHandler() {
      this.rotationAddDialog.rotationId = "";
      this.rotationAddDialog.addType = 1;
      this.rotationAddDialog.open = true;
    },
    queryHandler() {
      this.queryParams.pages = 1;
      this.getList();
    },
    getList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl('/app/mini/project/rotation/lists'),
        method: 'get',
        params: this.$http.adornParams({
          page: this.queryParams.pages,
          limit: this.queryParams.sizes
        })
      }).then(({
                 data
               }) => {
        if (data && data.code === 0) {
          this.dataListLoading = false
          this.dataList = data.page.list;
          this.totalCount = data.page.totalCount;
        }
      })
    },
    updateHandler(rotationId) {
      this.rotationAddDialog.rotationId = rotationId + '';
      this.rotationAddDialog.addType = 2;
      this.rotationAddDialog.open = true;
    },
    deleteHandler(rotationId) {
      this.$http({
        url: this.$http.adornUrl('/app/mini/project/rotation/delete/' + rotationId),
        method: 'get'
      }).then(({
                 data
               }) => {
        if (data && data.code === 0) {
          this.$message.success("删除成功");
          this.queryHandler();
        }
      })
    }
  }
}
</script>

<style>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}

.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}

.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
</style>
