<template>
  <div>
    <div style="padding: 15px; border-bottom: 1px solid #ddd">
      <el-button type="danger" plain @click="delBatch">取消分享</el-button>
    </div>

    <el-table :data="tableData" style="font-size: 15px" stripe @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55" align="center"></el-table-column>
      <el-table-column label="文件名称" style="font-size: 10px">
        <template v-slot="scope">
          <div style="display: flex; cursor: pointer"  @mouseenter="mouseEnter(scope.row)" @mouseleave="mouseLeave(scope.row)">
            <div style="flex: 1">
              <i style="color: #409EFF; font-size: 23px" :class="typeList.find(v => v.text === scope.row.type)?.icon || 'el-icon-file'"></i>
              <span v-if="!scope.row.unSave" style="margin-left: 5px">{{ scope.row.name }}</span>
            </div>
            <div style="color: #409EFF; font-size: 23px" v-if="scope.row.optShow">
              <el-tooltip content="取消分享" effect="light" :open-delay="1000">
                <i class="el-icon-remove-outline" style="margin-right: 10px; cursor: pointer" @click="del(scope.row.id)"></i>
              </el-tooltip>
              <el-tooltip content="复制链接" effect="light" :open-delay="1000">
                <i class="el-icon-copy-document" style="margin-right: 10px; cursor: pointer" @click="copy(scope.row)"></i>
              </el-tooltip>
            </div>
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="shareTime" label="分享时间" width="300"></el-table-column>
      <el-table-column prop="status" label="状态" width="300">
        <template v-slot="scope">
          <span style="color: red" v-if="scope.row.status.includes('小时')"> {{ scope.row.status }} </span>
          <span v-else> {{ scope.row.status }} </span>
        </template>
      </el-table-column>
      <el-table-column prop="size" label="访问次数" width="200"></el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  name: "ShareComponent",
  props: {
    typeList: null
  },
  data() {
    return {
      tableData: []
    }
  },
  created() {
    this.load()
  },
  methods: {
    copy(row) {
      let currentUrl = location.href.substring(0, location.href.lastIndexOf('/front'))
      let shareLink = currentUrl + '/front/openShare?code=' + row.code + '&shareId=' + row.id

      let _input = document.createElement("input");   // 直接构建input
      _input.value = shareLink;  // 设置内容
      document.body.appendChild(_input);    // 添加临时实例
      _input.select();   // 选择实例内容
      document.execCommand("Copy");   // 执行复制
      document.body.removeChild(_input)
      this.$message.success("复制成功")
    },
    del(id) {   // 单个删除
      this.$confirm('您确定取消分享吗？', '确认取消分享', {type: "warning"}).then(response => {
        this.$request.delete('/share/delete/' + id).then(res => {  // 这里传的是 trash的id  要传file_id
          if (res.code === '200') {   // 表示操作成功
            this.$message.success('操作成功')
            this.load()
          } else {
            this.$message.error(res.msg)  // 弹出错误的信息
          }
        })
      }).catch(() => {
      })
    },
    handleSelectionChange(rows) {   // 当前选中的所有的行数据
      this.ids = rows.map(v => v.id)   //  [1,2]
    },
    delBatch() {   // 批量取消分享
      if (!this.ids.length) {
        this.$message.warning('请选择数据')
        return
      }
      this.$confirm('您确定批量取消分享这些数据吗？', '确认取消分享', {type: "warning"}).then(response => {
        this.$request.delete('/share/delete/batch', {data: this.ids}).then(res => {
          if (res.code === '200') {   // 表示操作成功
            this.$message.success('操作成功')
            this.load()
          } else {
            this.$message.error(res.msg)  // 弹出错误的信息
          }
        })
      }).catch(() => {
      })
    },
    load() {
      this.$request.get('/share/selectAll').then(res => {
          this.tableData = res.data || []
      })
    },
    mouseEnter(row) {
      this.$set(row, 'optShow', true)
    },
    mouseLeave(row) {
      this.$set(row, 'optShow', false)
    }
  }
}
</script>

<style scoped>

</style>