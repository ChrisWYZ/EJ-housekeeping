<template>
    <div id="app">
        <!-- 面包屑 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>栏目管理</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- 面包屑 -->
        <!-- 标题 -->
        <h1>{{message}}</h1>
        <!-- 标题 -->
<!-- 按钮组           -->
          <el-row>
              <el-col :span="18">
                    <el-form :inline="true" class="demo-form-inline">
                        <el-form-item label="栏目名称">
                            <el-input  placeholder="请输入栏目名称查询" v-model="param.name" clearable></el-input>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="primary" @click="searchByName">查询</el-button>
                        </el-form-item>
                    </el-form>
              </el-col>
                <el-col :span="6" style="text-align: right;">
                    <el-button type="primary" @click="openDialog">添加</el-button>
                </el-col>
          </el-row>
<!-- 按钮组           -->


<!-- 表格 -->
        <el-table :data="categorys.list" style="width: 100%;">
            <el-table-column prop="id" label="#"></el-table-column>
            <el-table-column prop="name" label="栏目名"></el-table-column>
            <el-table-column prop="num" label="数目" ></el-table-column>
            <el-table-column prop="icon" label="图标" >
                <template #default="record">
                    <img :src="record.row.icon">
                </template>
            </el-table-column>
            <el-table-column prop="parentId" label="父栏目" ></el-table-column>
            <el-table-column label="操作" >
                <template slot-scope="scope">
                    <el-button type="primary" @click="updateHandler(scope.row)">修改</el-button>
                    <el-button type="danger" @click.prevent="deleteHandler(scope.$index,scope.row.id)">删除</el-button>    
                </template>
            </el-table-column>
        </el-table>
<!-- 表格 -->



<!-- 模态框 -->
        <el-dialog title="添加栏目信息" :visible.sync="dialogFormVisible">
        <el-form :model="category">
            <el-form-item label="栏目名" :label-width="formLabelWidth">
            <el-input v-model="category.name" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="数目" :label-width="formLabelWidth">
            <el-input v-model="category.num" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="图标" :label-width="formLabelWidth">
            <el-input v-model="category.icon" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="parentId" :label-width="formLabelWidth">
            <el-input v-model="category.parentId" autocomplete="off"></el-input>
            </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitHandler(category)">提 交</el-button>
        </div>
        </el-dialog>
<!-- 模态框 -->


<!-- 分页 -->
        <el-pagination
            layout="prev, pager, next"
            :current-page="param.page+1"
            :page-size="param.pageSize"
            :total="categorys.total"
            @current-change="pageChange">
        </el-pagination>
<!-- 分页 -->


    </div>

    
</template>


<script>
import axios from 'axios'
//配置axios的地址
axios.defaults.baseURL = 'http://134.175.154.93:6677';
axios.defaults.headers.common["Content-Type","application/x-www-form-urlencoded"];
axios.defaults.transformRequest = [(data)=>{
    // 将data转换为查询字符串 qs
    let str = ""
    for(let key in data){
        let val = data[key];
        str +=( key+"="+val+"&")
    }
    return str.slice(0,str.length-1);
}]
export default {
    name:'app',
    data(){
        return{
            message:"栏目管理",
            categorys:{
                list:[]
            },
            category:{},
            dialogFormVisible: false,
            formLabelWidth: '120px',
            param:{
                name:"",
                page:0,
                pageSize:5
            }
        }
    },
    created(){
        this.reloadData();
    },
    methods:{
        searchByName(){
            this.reloadData();
        },
        pageChange(page){
            this.param.page = page-1;
            this.reloadData();
        },
        openDialog(){
            this.category={}
            this.dialogFormVisible = true;
        },
        async reloadData(){
            let response = await axios.post("/category/query",this.param)
            this.categorys = response.data.data
        },
        updateHandler(row){
            this.dialogFormVisible = true;
            this.category=row;            
        },
        async submitHandler(category){
            let data = this.category
            let response = await axios.post("/category/saveOrUpdate",data);
            this.dialogFormVisible = false;
            if(response.data.status===200){
                this.$message({
                    type:'success',
                    message:'保存成功'
                });
            }else{
                this.$message({
                    type:'danger',
                    message:'保存失败'
                })
            }
            this.reloadData();

        },
        async deleteHandler(index,id){
            let response = await axios.get("/category/deleteById",{params:{id:id}});
            let status = response.data.status;
            let message = response.data.message;
            if (status===200){
                this.$message({
                type: 'success',
                message: '删除成功!'
                });
                this.reloadData();
            }else{
                this.$message({
                type: 'warning',
                message: '删除失败'
                });
            };

        },
        
    }

}
</script>

<style scoped>
    #app {
        position: relative;
    }
    h1 {
        margin: 0;
        padding: 0;
        color:rgb(78, 11, 11);
        text-align: center
    }
    .el-pagination {
        position: absolute;
        left: 45%;
    }
</style>