<template>
  <el-table :data="dataList" border stripe v-loading="dataListLoading"
            style="width: 100%;">
    <el-table-column prop="categoryTitle" header-align="center" align="center" width="250" label="鉴定门类"
                     :show-overflow-tooltip="true"></el-table-column>
    <el-table-column prop="categoryPrice" label="鉴定费用" align="center" width="250">
      <template slot-scope="scope">
        <el-input v-model="scope.row.categoryPrice" :disabled="scope.row.disabledState"></el-input>
      </template>
    </el-table-column>
    <el-table-column label="操作" align="center" width="300">
      <template slot-scope="scope">
        <el-button size="mini" type="text"
                   @click="updateHandler(scope.$index)">编 辑
        </el-button>
        <el-button size="mini" type="text"
                   @click="subHandler(scope.row,scope.$index)">保 存
        </el-button>
      </template>
    </el-table-column>
  </el-table>
</template>


<script>
export default {
  data() {
    return {
      dataList: [],
      dataListLoading: true,
      isEditObj: []
    }
  },
  created() {
    this.getList();
  },
  methods: {
    getList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl('/sys/appraisalCategory/lists'),
        method: 'get'
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.dataList = data.data
          this.dataListLoading = false;
        }
      })
    },
    subHandler(row, index) {
      this.$http({
        url: this.$http.adornUrl('/sys/appraisalCategory/save'),
        method: 'get',
        params: this.$http.adornParams({
          id: row.id,
          categoryPrice: row.categoryPrice
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.$message.success("编辑成功")
          this.dataList[index].disabledState = true;
        }
      })
    },
    updateHandler(index) {
      this.dataList[index].disabledState = false;
    }
  }
}
</script>
