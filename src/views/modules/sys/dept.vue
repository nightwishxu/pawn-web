<template>
  <el-row :gutter="20">
    <el-col :span="6">
      <div class="grid-content bg-purple">
        <el-tree :data="treeData" node-key="id" :default-expand-all="true" :expand-on-click-node="false" :props="defaultProps"
          @node-click="nodeClick">
          <span class="custom-tree-node" slot-scope="{ node, data }" @mouseleave="mouseleave(data, $event)" @mouseover="mouseover(data, $event)">
            <span :class="[data.status === '0' ? 'disableMenu' : '']">
              <i :class="data.icon"></i>&nbsp;{{ data.name }}
            </span>
            <span class="menunone">
              <el-tooltip class="item" effect="dark" :content="$t('m.add')+$t('m.sameLevel')" placement="top">
                <el-button v-if="data.parentId !== '0'" type="text" size="mini" class="el-icon-plus" @click="addSame(data.id)"></el-button>
              </el-tooltip>
              <el-tooltip class="item" effect="dark" :content="$t('m.add')+$t('m.nextLevel')" placement="top">
                <el-button type="text" size="mini" class="el-icon-circle-plus-outline" @click="addSub(data.id, data.name)"></el-button>
              </el-tooltip>
              <el-tooltip class="item" effect="dark" :content="$t('m.edit')" placement="top">
                <el-button type="text" size="mini" class="el-icon-edit" @click="updateDept(data.id)"></el-button>
              </el-tooltip>
              <el-tooltip class="item" effect="dark" :content="$t('m.delete')" placement="top">
                <el-button type="text" v-if="data.parentId !== '0'" size="mini" class="el-icon-delete" @click="deleteDept(data.id)"></el-button>
              </el-tooltip>
            </span>
          </span>
        </el-tree>
      </div>
    </el-col>
    <el-col :span="18" style="height: 500px;">
      <div class="grid-content bg-purple">
        <el-table :data="tableData" border stripe v-loading="listLoading" style="width: 100%;">
          <el-table-column prop="user_id" header-align="center" align="center" width="80" label="ID"></el-table-column>
          <el-table-column prop="username" header-align="center" align="center" :label="$t('m.userName')"></el-table-column>
          <el-table-column prop="email" header-align="center" align="center" :label="$t('m.email')"></el-table-column>
          <el-table-column prop="mobile" header-align="center" align="center" :label="$t('m.phone')"></el-table-column>
          <el-table-column prop="create_time" header-align="center" align="center" width="180" :label="$t('m.creatTime')"></el-table-column>
        </el-table>
        <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
          :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage" layout="total, sizes, prev, pager, next, jumper"></el-pagination>
        <add-or-update ref="addOrUpdate"></add-or-update>
      </div>
    </el-col>
  </el-row>
</template>

<script>
  import AddOrUpdate from './dept-add-or-update'
  export default {
    data() {
      return {
        tableData: [],
        treeData: [],
        defaultProps: {
          children: 'children',
          label: 'name'
        },
        clickNode: null,
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        listLoading: true,
        params: {
          dialog: false,
          id: '',
          title: '',
          name: ''
        }
      }
    },
    components: {
      AddOrUpdate
    },
    provide() {
      return {
        restTable: this.loadDept
      }
    },
    created() {
      this.loadDept()
      this.loadTable()
    },
    methods: {
      loadDept() {
        let _this = this
        this.$http({
          url: this.$http.adornUrl('/sys/dept/list'),
          method: 'get',
          data: this.$http.adornData()
        }).then(({
          data
        }) => {
          if (data.code === 0) {
            _this.treeData = data.data
          }
        })
      },
      mouseleave(data, $event) {
        $event.currentTarget.firstElementChild.nextElementSibling.setAttribute('class', 'menunone')
      },
      mouseover(data, $event) {
        $event.currentTarget.firstElementChild.nextElementSibling.setAttribute('class', 'menushow')
      },
      addSame(id) {
        let _this = this
        this.$nextTick(() => {
          _this.$refs.addOrUpdate.init(id, '', this.$t('m.add') + this.$t('m.sameLevel') + this.$t('m.dept'),
            'addSame')
        })
      },
      addSub(id, name) {
        let _this = this
        this.$nextTick(() => {
          _this.$refs.addOrUpdate.init(id, name, this.$t('m.add') + this.$t('m.nextLevel') + this.$t('m.dept'),
            'addSub')
        })
      },
      updateDept(id) {
        let _this = this
        this.$nextTick(() => {
          _this.$refs.addOrUpdate.init(id, '', this.$t('m.edit') + this.$t('m.dept'), 'updateMenu')
        })
      },
      deleteDept(id) {
        this.$confirm(this.$t('dept.delete'), this.$t('dept.deletePrompt'), {
          type: 'warning',
          dangerouslyUseHTMLString: true
        }).then(() => {
          let _this = this
          this.$http({
            url: this.$http.adornUrl(`/sys/dept/delete/${id}`),
            method: 'post',
            data: this.$http.adornData()
          }).then(({
            data
          }) => {
            if (data.code === 0) {
              _this.clickNode = null
              _this.pageIndex = 1
              _this.pageSize = 10
              _this.loadTable()
              _this.loadDept()
            }
          })
        }).catch(() => {})
      },
      loadTable() {
        let _this = this
        let dept_id = this.clickNode === null ? "" : this.clickNode.id
        this.$http({
          url: this.$http.adornUrl("/sys/dept/findUserByDept"),
          method: 'get',
          params: this.$http.adornParams({
            page: this.pageIndex,
            limit: this.pageSize,
            dept_id: dept_id
          })
        }).then(({
          data
        }) => {
          if (data.code === 0) {
            _this.listLoading = false
            _this.tableData = data.page.list
            _this.totalPage = data.page.totalCount
          }
        })
      },
      nodeClick(data, node, obj) {
        if (data.parentId === '0') {
          this.clickNode = null
        } else {
          this.clickNode = data
        }
        this.pageIndex = 1
        this.pageSize = 10
        this.loadTable()
      },
      // 每页数
      sizeChangeHandle(val) {
        this.pageSize = val
        this.pageIndex = 1
        this.loadTable()
      },
      // 当前页
      currentChangeHandle(val) {
        this.pageIndex = val
        this.loadTable()
      }
    }
  }
</script>
<style scoped>
  .page-container,
  .el-container,
  .second-aside,
  .second-aside>.bg {
    height: 100%;
  }
</style>
<style>
  .custom-tree-node {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 14px;
    padding-right: 8px;
  }

  .menushow {
    display: block;
  }

  .menunone {
    display: none;
  }

  .app-container .el-main {
    padding: 10px 10px 10px 10px;
    height: 100%;
  }

  .disableMenu {
    color: red;
  }
</style>
