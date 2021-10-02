<template>
  <div style="padding: 10px">
    <!--    功能区域-->
    <div style="margin: 10px 0">
      <el-button type="primary" @click="add">新增</el-button>
      <el-button type="primary">导入</el-button>
      <el-button type="primary">导出</el-button>
    </div>
    <!--    搜索区域-->
    <div style="margin:10px 0">
      <el-input v-model="search" placeholder="请输入关键字" style="width: 20%"></el-input>
      <el-button type="primary" style="margin-left: 5px" @click="">查询</el-button>
    </div>
    <!--    显示查询数据-->
    <el-table
        :data="tableData"
        border
        stripe
        style="width: 100%">
      <el-table-column
          prop="id"
          label="ID"
          sortable
      >
      </el-table-column>
      <el-table-column
          prop="title"
          label="标题">
      </el-table-column>
      <el-table-column
          prop="author"
          label="作者">
      </el-table-column>
      <el-table-column
          prop="time"
          label="时间">
      </el-table-column>
      <el-table-column
          fixed="right"
          label="操作"
      >
        <template #default="scope">
          <el-button size="min" @click="details(scope.row)" >详情</el-button>
          <el-button size="min" @click="handleEdit(scope.row)" >编辑</el-button>
          <el-popconfirm title="确认删除吗？" @confirm="handleDelete(scope.row.id)">
            <template #reference>
              <el-button size="mini" type="danger">删除</el-button>
            </template>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
    <div style="margin: 10px 0">
      <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="[5, 10, 20]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
      </el-pagination>

      <el-dialog title="提示" v-model="dialogVisible" width="30%">
        <el-form :model="form" label-width="120px">
          <el-form-item label="标题">
            <el-input v-model="form.title" style="width: 50%"></el-input>
          </el-form-item>
          <div id="div1"></div>
        </el-form>

        <template #footer>
          <span class="dialog-footer">
            <el-button @click="dialogVisible=false"> 取消</el-button>
            <el-button type="primary" @click="save">确定</el-button>
          </span>
        </template>
      </el-dialog>

      <el-dialog title="提示" v-model="vis" width="50%">
        <el-card>
          <div v-html="detail.content" style="min-height: 100px"></div>
        </el-card>
      </el-dialog>

    </div>
  </div>
</template>

<script>


import request from "../../utils/request";
import E from 'wangeditor'
let editor

export default {
  name: 'News',
  components: {

  },
  data(){
    return {
      form:{},
      dialogVisible:false,
      search:'',
      pageSize:10,
      currentPage:1,
      total:10,
      tableData:[],
      vis:false,
      detail:{}
    }
  },
  created() {
    this.load()
  },
  methods:{
    //获取当前行对象，detail.content即表示新闻内容
    details(row){
      this.detail=row
      this.vis=true
    },
    //表单上传文件仅仅进行了个操作，并没有记录上传文件的相关信息，我们手动给表单添加上传文件的url信息
    fileUploadSuccess(res){
      this.form.cover=res.data
    },
    load(){
      request.get("/news",{
        params:{
          pageNum:this.currentPage,
          pageSize:this.pageSize,
          search:this.search
        }
      }).then(res=>{
        this.tableData=res.data.records
        this.total=res.data.total
      })
    },
    add(){
      this.dialogVisible=true
      this.form={}
      this.$nextTick(()=>{
        if (!editor) {
          editor = new E('#div1')
          editor.config.uploadImgServer = 'http://localhost:9090/files/editor/upload'
          editor.config.uploadFileName = "file"
          editor.create()
        }
        editor.txt.html("")
      })
    },
    save(){
      //wangeditor只是提供了一个有编辑功能的窗口，要想保存窗口的内容要么窗口输入与form.content关联，要么获取窗口内容直接给form.content赋值
      this.form.content = editor.txt.html()  // 获取 编辑器里面的值，然后赋予到实体当中
      if(this.form.id){
        request.put("/news",this.form).then(res=>{
          console.log(res)
          if(res.code==='0'){
            this.$message({
              type:"success",
              message:"更新成功"
            })
          } else{
            this.$message({
              type:"error",
              message:"更新失败"
            })
          }
        })
      }
      else {
        this.form.content=editor.txt.html() //获取编辑器里面的值，赋予到form中
        let userStr=sessionStorage.getItem('user')||"{}"
        let user=JSON.parse(userStr)
        this.form.author=user.nickName
        request.post("/news", this.form).then(res => {
          if(res.code==='0'){
            this.$message({
              type:"success",
              message:"新增成功"
            })
          } else{
            this.$message({
              type:"error",
              message:"新增失败"
            })
          }
        })
      }
      this.load()
      this.dialogVisible=false
    },
    handleEdit(row){
      // this.form=JSON.parse(JSON.stringify(row))
      // this.dialogVisible=true
      // this.$nextTick(()=>{
      //   if (!editor) {
      //     editor = new E('#div1')
      //     editor.config.uploadImgServer = 'http://localhost:9090/files/editor/upload'
      //     editor.config.uploadFileName = "file"
      //     editor.create()
      //   }
      //   editor.txt.html(row.content)
      // })
      this.form = JSON.parse(JSON.stringify(row))
      this.dialogVisible = true

      this.$nextTick(() => {
        // 关联弹窗里面的div，new一个 editor对象
        // 关联弹窗里面的div，new一个 editor对象
        if (!editor) {
          editor = new E('#div1')

          // 配置 server 接口地址
          editor.config.uploadImgServer = 'http://localhost:9090/files/editor/upload'
          editor.config.uploadFileName = "file"  // 设置上传参数名称
          editor.create()
        }

        editor.txt.html(row.content)
      })
    },
    handleDelete(id){
      request.delete("/news/"+id).then(res=>{
        if(res.code==='0'){
          this.$message({
            type:"success",
            message:"删除成功"
          })
        } else{
          this.$message({
            type:"error",
            message:"删除失败"
          })
        }
      })
      this.load()
    },
    handleSizeChange(pageSize){//改变每页个数触发
      this.pageSize=pageSize
      this.load()
    },
    handleCurrentChange(pageNum){//改变当前页码触发
      this.currentPage=pageNum
      this.load()
    }
  }
}
</script>
