<template>
  <el-dialog title="选择专家" :close-on-click-modal="false" :visible.sync="visible">
    <el-table :data="dataList" border stripe v-loading="dataListLoading" style="width: 100%;">
      <el-table-column prop="id" header-align="center" align="center" width="80" label="ID" v-if="false"></el-table-column>
      <el-table-column prop="expertName" header-align="center" align="center" label="专家姓名"></el-table-column>
      <el-table-column prop="state" header-align="center" align="center" label="状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.state === '1'">等待鉴定</el-tag>
          <el-tag v-else-if="scope.row.state === '2'" type="success">鉴定完成</el-tag>
          <el-tag v-else-if="scope.row.state === '3'" type="danger">无法鉴定</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="name" header-align="center" align="center" label="名称"></el-table-column>
      <el-table-column prop="size" header-align="center" align="center" label="尺寸"></el-table-column>
      <el-table-column prop="weight" header-align="center" align="center" label="重量"></el-table-column>
      <el-table-column prop="main_material" header-align="center" align="center" label="主材质"></el-table-column>
      <el-table-column prop="sub_material" header-align="center" align="center" label="副材质"></el-table-column>
      <el-table-column prop="years" header-align="center" align="center" label="年代"></el-table-column>
      <el-table-column prop="other" header-align="center" align="center" label="其他"></el-table-column>
      <el-table-column prop="market_liquidity" header-align="center" align="center" label="市场流通性" width="120"></el-table-column>
      <el-table-column prop="value_stability" header-align="center" align="center" label="价值稳定性"  width="120"></el-table-column>
      <el-table-column prop="material_vulnerability" header-align="center" align="center" label="材质易损性" width="120"></el-table-column>
      <el-table-column prop="pawn_price" header-align="center" align="center" label="典当价格"></el-table-column>
      <el-table-column prop="time" header-align="center" align="center" label="鉴定时间" width="180"></el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" v-if="state === '1' || state === '5'" :label="$t('m.operation')">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="chooseResult(scope.row.state, scope.row.id)">选择结果</el-button>
        </template>
      </el-table-column>
    </el-table>
    <edit-result ref="editResult" @closeAndRefresh="closeAndRefresh"></edit-result>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">{{ $t('m.cancel') }}</el-button>
    </span>
  </el-dialog>
</template>

<script>
import EditResult from './editResult';
export default {
  data() {
    return {
      visible: false,
      appraisalId: null,
      dataList: [],
      dataListLoading: false,
      state: ''
    };
  },
  components: {
    EditResult
  },
  methods: {
    init(id, state) {
      this.visible = true;
      this.appraisalId = id;
      this.dataListLoading = true;
      this.state = state;
      this.$http({
        url: this.$http.adornUrl('/appraisal/result/list'),
        method: 'get',
        params: this.$http.adornParams({
          appraisalId: this.appraisalId
        })
      }).then(({ data }) => {
        if (data && data.code === 0) {
          console.log(data);
          this.dataList = data.list;
        } else {
          this.dataList = [];
        }
        this.dataListLoading = false;
      });
    },
    closeAndRefresh() {
      this.visible = false;
      this.$emit('refreshDataList');
    },
    // 表单提交
    chooseResult(state, id) {
      if (state === '1') {
        this.$message.error('等待专家鉴定后才能选择！');
        return false;
      } else if (state === '3') {
        this.$confirm('是否选择该专家的建议？', '提示', {
          confirmButtonText: this.$t('prompt.yes'),
          cancelButtonText: this.$t('m.cancel'),
          type: 'warning'
        })
          .then(() => {
            this.$http({
              url: this.$http.adornUrl(`/appraisal/choose/${this.appraisalId}/${id}`),
              method: 'get',
              data: this.$http.adornParams()
            }).then(({ data }) => {
              if (data && data.code === 0) {
                this.$message({
                  message: this.$t('prompt.success'),
                  type: 'success',
                  onClose: () => {
                    this.visible = false;
                    this.$emit('refreshDataList');
                  }
                });
              } else {
                this.$message.error(data.msg);
              }
            });
          })
          .catch(() => {});
      } else if (state === '2') {
        this.$nextTick(() => {
          this.$refs.editResult.init(id, this.appraisalId);
        });
      }
    }
  }
};
</script>
