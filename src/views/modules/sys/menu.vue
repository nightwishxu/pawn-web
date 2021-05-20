<template>
  <div class="mod-menu">
    <el-form :inline="true" :model="dataForm">
      <el-form-item><el-button v-if="isAuth('sys:menu:save')" type="primary" @click="addOrUpdateHandle()">{{$t('m.add')}}</el-button></el-form-item>
    </el-form>

    <el-table :data="dataList" row-key="menuId" border style="width: 100%; ">
      <el-table-column prop="name" header-align="center" min-width="150" :label="$t('m.name')"></el-table-column>
      <el-table-column prop="parentName" header-align="center" align="center" width="120" :label="$t('m.parentMenu')"></el-table-column>
      <el-table-column header-align="center" align="center" :label="$t('m.icon')">
        <template slot-scope="scope">
          <icon-svg :name="scope.row.icon || ''"></icon-svg>
        </template>
      </el-table-column>
      <el-table-column prop="type" header-align="center" align="center" :label="$t('m.type')">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.type === 0" size="small">{{$t('m.catalog')}}</el-tag>
          <el-tag v-else-if="scope.row.type === 1" size="small" type="success">{{$t('m.menu')}}</el-tag>
          <el-tag v-else-if="scope.row.type === 2" size="small" type="info">{{$t('m.button')}}</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="orderNum" header-align="center" align="center" :label="$t('m.sort')"></el-table-column>
      <el-table-column prop="url" header-align="center" align="center" width="150" :show-overflow-tooltip="true" :label="$t('m.menuUrl')"></el-table-column>
      <el-table-column prop="perms" header-align="center" align="center" width="150" :show-overflow-tooltip="true" :label="$t('m.permissionsCode')"></el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" :label="$t('m.operation')">
        <template slot-scope="scope">
          <el-button v-if="isAuth('sys:menu:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.menuId)">{{$t('m.edit')}}</el-button>
          <el-button v-if="isAuth('sys:menu:delete')" type="text" size="small" @click="deleteHandle(scope.row.menuId)">{{$t('m.delete')}}</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from './menu-add-or-update'
import { treeDataTranslate } from '@/utils'
export default {
  data() {
    return {
      dataForm: {},
      dataList: [],
      dataListLoading: false,
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
        url: this.$http.adornUrl('/sys/menu/list'),
        method: 'get',
        params: this.$http.adornParams()
      }).then(({ data }) => {
        this.dataList = treeDataTranslate(data, 'menuId')
        this.dataListLoading = false
      })
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
      this.$confirm(this.$t('dict.delete'), this.$t('prompt.prompt'), {
        confirmButtonText: this.$t('prompt.yes'),
        cancelButtonText: this.$t('m.cancel'),
        type: 'warning'
      })
        .then(() => {
          this.$http({
            url: this.$http.adornUrl(`/sys/menu/delete/${id}`),
            method: 'post',
            data: this.$http.adornData()
          }).then(({ data }) => {
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
        })
        .catch(() => {})
    }
  }
}
</script>
