<template>
  <div>
    <div style="padding: 15px; border-bottom: 1px solid #ddd">
      <el-button type="danger" plain @click="delBatch">批量删除</el-button>
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
              <el-tooltip content="删除" effect="light" :open-delay="1000">
                <i class="el-icon-delete" style="margin-right: 10px; cursor: pointer" @click="del(scope.row.fileId)"></i>
              </el-tooltip>
              <el-tooltip content="还原" effect="light" :open-delay="1000">
                <i class="el-icon-refresh" style="margin-right: 10px; cursor: pointer" @click="restore(scope.row.fileId)"></i>
              </el-tooltip>
            </div>
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="updateTime" label="删除时间" width="300"></el-table-column>
      <el-table-column prop="size" label="文件大小（KB）" width="300"></el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  name: "Trash",
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
    restore(fileId) {
      this.$request.put('/diskFiles/restore/' + fileId).then(res => {
        if (res.code === '200') {   // 表示操作成功
          this.$message.success('操作成功')
          this.load()
        } else {
          this.$message.error(res.msg)  // 弹出错误的信息
        }
      })
    },
    del(fileId) {   // 单个删除
      this.$confirm('您确定删除吗？', '确认删除', {type: "warning"}).then(response => {
        this.$request.delete('/diskFiles/delete/' + fileId).then(res => {  // 这里传的是 trash的id  要传file_id
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
      this.ids = rows.map(v => v.fileId)   //  [1,2]
    },
    delBatch() {   // 批量删除
      if (!this.ids.length) {
        this.$message.warning('请选择数据')
        return
      }
      this.$confirm('您确定批量删除这些数据吗？', '确认删除', {type: "warning"}).then(response => {
        this.$request.delete('/diskFiles/delete/batch', {data: this.ids}).then(res => {
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
      this.$request.get('/diskFiles/selectTrash').then(res => {
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