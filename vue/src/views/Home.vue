<template>
  <div style="padding: 10px">
    <div style="margin: 10px 0">
      <el-button type="primary">新增</el-button>
      <el-button type="primary">导入</el-button>
      <el-button type="primary">导出</el-button>
    </div>
    <div style="margin:10px 0">
      <el-input v-model="search" placeholder="请输入关键字" style="width: 20%"></el-input>
      <el-button type="primary" style="margin-left: 5px">查询</el-button>
    </div>
    <el-table
        :data="tableData"
        border
        stripe
        style="width: 100%">
      <el-table-column
          prop="date"
          label="Date"
          sortable
      >
      </el-table-column>
      <el-table-column
          prop="name"
          label="Name">
      </el-table-column>
      <el-table-column
          prop="address"
          label="Address">
      </el-table-column>
      <el-table-column
          fixed="right"
          label="操作"
      >
        <template #default="scope">
          <el-button size="min" @click="handleEdit(scope.$index,scope.row)" >编辑</el-button>
          <el-popconfirm
              title="确认删除吗？"
          >
            <template #reference>
              <el-button size="mini" @click="handleDelete(scope.$index,scope.row)" type="danger">删除</el-button>
            </template>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
    <div style="margin: 10px 0">
      <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage4"
          :page-sizes="[5, 10, 20]"
          :page-size="10"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
      </el-pagination>
    </div>
  </div>
</template>

<script>


export default {
  name: 'Home',
  components: {

  },
  data(){
    return {
      search:'',
      currentPage:1,
      total:10,
      tableData:[
        {
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-04',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1517 弄'
        }, {
          date: '2016-05-01',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1519 弄'
        }, {
          date: '2016-05-03',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        }
      ]
    }
  },
  methods:{
    handleEdit(){

    },
    handleDelete(){

    },
    handleSizeChange(){

    },
    handleCurrentChange(){

    }
  }
}
</script>
