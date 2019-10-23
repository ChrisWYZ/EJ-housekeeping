<template>
    <div id="app">
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>评论管理</el-breadcrumb-item>
        </el-breadcrumb>
        <h1>{{message}}</h1>
        <el-button type="primary" @click="openDialog">添加</el-button>
        <el-table :data="comments" style="width: 100%;">
            <el-table-column prop="id" label="#"></el-table-column>
            <el-table-column prop="content" label="评论内容" ></el-table-column>
            <el-table-column prop="commentTime" label="评论时间" ></el-table-column>
            <el-table-column prop="orderId" label="订单号" ></el-table-column>
            <el-table-column label="操作" >
                <template slot-scope="scope">
                    <el-button type="primary" @click="updateHandler(scope.row)">修改</el-button>
                    <el-button type="danger" @click.prevent="deleteHandler(scope.$index,scope.row.id)">删除</el-button>    
                </template>
            </el-table-column>
        </el-table>



        <el-dialog title="添加评论信息" :visible.sync="dialogFormVisible">
        <el-form :model="comment">
            <el-form-item label="评论内容" :label-width="formLabelWidth">
            <el-input type="textarea" v-model="comment.content" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="评论时间" :label-width="formLabelWidth">
            <el-input v-model="comment.commentTime" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="订单号" :label-width="formLabelWidth" >
            <!-- <el-input v-model="comment.orderId" autocomplete="off"></el-input> -->
                <el-select v-model="comment.orderId" placeholder="请选择订单号" >
                    <el-option v-for="(item, index) in orders" :key="index" :label="item.id" :value="item.id"></el-option>
                </el-select>
            </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitHandler(comment)">提 交</el-button>
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
            message:"评论管理",
            comments:[],
            comment:{},
            dialogFormVisible: false,
            formLabelWidth: '120px',
            orders:[],
        }
    },
    created(){
        this.reloadData();
        this.selectOrderId();
    },
    methods:{
        openDialog(){
            this.comment={}
            this.dialogFormVisible = true;
        },
        reloadData(){
            axios.get("/comment/findAll").then((response)=>{
                this.comments=response.data.data;
            })
        },
        updateHandler(row){
            this.dialogFormVisible = true;
            this.comment=row;            
        },
        async submitHandler(comment){
            let data = this.comment
            let response = await axios.post("/comment/saveOrUpdate",data);
            if(response.data.status===200){
                this.dialogFormVisible = false;
                this.$message({
                    type:'success',
                    message:'保存成功！'
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
            let response = await axios.get("/comment/deleteById",{params:{id:id}});
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
        selectOrderId(){
            axios.get("/order/findAll").then((response)=>{
                this.orders=response.data.data;
            })
        }
        
    }

}
</script>

<style scoped>
    h1 {
        margin: 0;
        padding: 0;
        color:rgb(78, 11, 11);
        text-align: center
    }
</style>