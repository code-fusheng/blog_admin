<template>
  <div>
    <el-button type="primary" class="add-button" size="mini" @click="openAddDrawer">添加</el-button>

    <!-- 列表 -->
    <el-table :data="typeList" border style="width: 100%">
      <el-table-column type="index" fixed="left" label="#" width="60" align="center" />
      <el-table-column prop="typeName" label="分类" width="200" align="center" />
      <el-table-column prop="typeBlogCount" label="博客数" width="100" align="center" />
      <el-table-column prop="" label="创建时间" width="180" align="center" sortable="custom" />
      <el-table-column prop="" label="更新时间" width="180" align="center" sortable="custom" />
      <el-table-column prop="enable" label="启用" width="80">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.enable === 1">启用</el-tag>
          <el-tag v-else type="info">未启用</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="360" align="center">
        <template slot-scope="scope">
          <el-button size="mini" type="primary" @click="handleEdit(scope.row.typeId)">编辑</el-button>
          <el-button v-if="scope.row.enable === 0" size="mini" type="success" @click="toEnable(scope.row.typeId)">启用</el-button>
          <el-button v-if="scope.row.enable === 1" size="mini" type="warning" @click="toDisable(scope.row.typeId)">弃用</el-button>
          <el-button size="mini" type="danger" @click="handleDelete(scope.row.typeId)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 添加抽屉 -->
    <el-drawer title="添加" :visible.sync="drawer">
      <type-add @getTypeList="getTypeList" @closeAddDrawer="closeAddDrawer" />
    </el-drawer>

    <!-- 修改弹窗 -->
    <el-dialog title="修改" :visible.sync="updateDialog">
      <type-update :type="type" @getTypeList="getTypeList" @closeUpdateDialog="closeUpdateDialog" />
    </el-dialog>
  </div>
</template>

<script>
import typeApi from '@/api/type'
import TypeAdd from './type-add'
import TypeUpdate from './type-update'
export default {
  components: {
    TypeAdd,
    TypeUpdate
  },
  data() {
    return {
      type: {}, // 分类
      updateDialog: false, // 控制修改弹窗展示
      drawer: false, // 控制添加抽屉展示
      typeList: [] // 类型数组
    }
  },
  created() {
    this.getTypeList()
  },
  methods: {
    getTypeList() {
      // 查询类型列表
      typeApi.listBack().then(res => {
        console.log(res.data)
        this.typeList = res.data
      })
    },
    handleEdit(id) {
      // 修改
      typeApi.get(id).then(res => {
        this.type = res.data
        this.updateDialog = true
      })
    },
    toEnable(id) {
      // 启用
      this.$confirm('是否启用该分类?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        typeApi.enableById(id).then(res => {
          this.$message.success(res.msg)
          this.getTypeList()
        })
      })
    },
    toDisable(id) {
      // 弃用
      this.$confirm('是否弃用该分类?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        typeApi.disableById(id).then(res => {
          this.$message.success(res.msg)
          this.getTypeList()
        })
      })
    },
    handleDelete(id) {
      // 删除
      this.$confirm('是否删除?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'error'
      }).then(() => {
        typeApi.deleteById(id).then(res => {
          this.$message.success(res.msg)
          this.getTypeList()
        })
      })
    },
    openAddDrawer() {
      // 打开添加弹窗
      this.drawer = true
    },
    closeAddDrawer() {
      // 关闭添加抽屉
      this.drawer = false
    },
    closeUpdateDialog() {
      // 关闭修改弹窗
      this.updateDialog = false
    }
  }
}
</script>
