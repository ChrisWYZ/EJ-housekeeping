<template>
    <div id="app">
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>顾客管理</el-breadcrumb-item>
        </el-breadcrumb>
        <h1>{{message}}</h1>
        <el-button type="primary" @click="openDialog">添加</el-button>
        <el-table :data="customers.list">
            <el-table-column prop="id" label="#"></el-table-column>
            <el-table-column prop="realname" label="姓名" ></el-table-column>
            <el-table-column prop="telephone" label="联系方式" ></el-table-column>
            <el-table-column prop="status" label="状态" ></el-table-column>
            <el-table-column label="操作" >
                <template slot-scope="scope">
                    <el-button type="primary" @click="updateHandler(scope.row)">修改</el-button>
                    <el-button type="danger" @click.prevent="deleteHandler(scope.$index,scope.row.id)">删除</el-button>    
                </template>
            </el-table-column>
        </el-table>
<!-- 分页 -->
        <el-pagination
            layout="prev, pager, next"
            :current-page="param.page+1"
            :page-size="param.pageSize"
            :total="customers.total"
            @current-change="pageChange">
        </el-pagination>
<!-- 分页 -->



        <el-dialog title="添加顾客信息" :visible.sync="dialogFormVisible">
        <el-form :model="customer">
            <el-form-item label="姓名" :label-width="formLabelWidth">
            <el-input v-model="customer.realname" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="联系方式" :label-width="formLabelWidth">
            <el-input v-model="customer.telephone" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="状态" :label-width="formLabelWidth">
            <el-input v-model="customer.status" autocomplete="off"></el-input>
            </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitHandler(customer)">提 交</el-button>
        </div>
        </el-dialog>

    </div>

    
</template>


<script>
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
import axios from 'axios'
export default {
    name:'app',
    data(){
        return{
            message:"顾客管理",
            customers:{
                list:[]
            },
            customer:{},
            dialogFormVisible: false,
            formLabelWidth: '120px',
            param:{
                name:'',
                page:0,
                pageSize:8
            }
        }
    },
    created(){
        this.reloadData();
    },
    methods:{
        pageChange(page){
            this.param.page = page-1;
            this.reloadData();
        },
        openDialog(){
            this.customer={}
            this.dialogFormVisible = true;
        },
        async reloadData(){
            let response = await axios.post("/customer/query",this.param)
                this.customers=response.data.data;
        },
        updateHandler(row){
            this.dialogFormVisible = true;
            this.customer=row;            
        },
        async submitHandler(customer){
            let data = this.customer
            let response = await axios.post("/customer/saveOrUpdate",data);
            if(response.data.status===200){
                this.dialogFormVisible = false;
                this.$message({
                    type:'success',
                    message:'保存成功'
                }); 
            }else{
                this.dialogFormVisible = false;
                this.$message({
                    type:'warning',
                    message:'保存失败！'
                }); 
            }
            this.reloadData();
        },
        async deleteHandler(index,id){
            let response = await axios.get("/customer/deleteById",{params:{id:id}});
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
    el-table{
        width: 100%;
        height: 100%;
    }
    .el-pagination {
        position: absolute;
        left: 500px;
    }
</style>