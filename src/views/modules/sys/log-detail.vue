<template>
  <el-dialog title="日志详情" :close-on-click-modal="false" :visible.sync="visible">
    <el-container>
      <el-header>用户信息</el-header>
      <el-container>
        <el-aside width="120px">用户名</el-aside>
        <el-main>{{username}}</el-main>
      </el-container>
      <el-container>
        <el-aside width="120px">IP地址</el-aside>
        <el-main>{{ip}}</el-main>
      </el-container>
      <el-container>
        <el-aside width="120px">浏览器</el-aside>
        <el-main>{{userAgent}}</el-main>
      </el-container>
    </el-container>
    <el-container>
      <el-header>操作信息</el-header>
      <el-container>
        <el-aside width="120px">请求方法</el-aside>
        <el-main>{{method}}</el-main>
      </el-container>
      <el-container>
        <el-aside width="120px">操作</el-aside>
        <el-main>{{operation}}</el-main>
      </el-container>
      <el-container>
        <el-aside width="120px">创建时间</el-aside>
        <el-main>{{createDate}}</el-main>
      </el-container>
      <el-container>
        <el-aside width="120px">执行时长(ms)</el-aside>
        <el-main>{{time}}</el-main>
      </el-container>
      <el-container>
        <el-aside width="120px">URL</el-aside>
        <el-main>{{url}}</el-main>
      </el-container>
      <el-container>
        <el-aside width="120px">返回状态</el-aside>
        <el-main>{{responseState}}</el-main>
      </el-container>
      <el-container>
        <el-aside width="120px">请求参数</el-aside>
        <el-main><pre>{{params}}</pre></el-main>
      </el-container>
    </el-container>

  </el-dialog>
</template>

<script>
  export default {
    data() {
      return {
        visible: false,
        username: '',
        ip: '',
        userAgent: '',
        method: '',
        operation: '',
        createDate: '',
        time: '',
        url: '',
        params: '',
        responseState: ''
      }
    },
    methods: {
      init(id) {
        this.visible = true
        this.$http({
          url: this.$http.adornUrl(`/sys/log/info/${id}`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({ data }) => {
          if (data && data.code === 0) {
            var log = data.log
            this.username = log.username
            this.ip = log.ip
            this.userAgent = log.userAgent
            this.method = log.method
            this.operation = log.operation
            this.createDate = log.createDate
            this.time = log.time
            this.url = log.url
            this.responseState = log.responseState
            this.params = JSON.stringify(JSON.parse(log.params), null, 2)
          }
        })
      }
    }
  }
</script>

<style>
  .el-header, .el-footer {
    background-color: #B3C0D1;
    color: #333;
    text-align: left;
    line-height: 60px;
  }

  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 55px;
  }

  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: left;
    line-height: 15px;
  }
</style>
