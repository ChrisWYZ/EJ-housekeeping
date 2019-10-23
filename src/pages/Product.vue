<template>
    <div id="app">
<!-- 面包屑 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>产品管理</el-breadcrumb-item>
        </el-breadcrumb>
<!-- 面包屑 -->
<!-- 标题 -->
        <h1>{{message}}</h1>
<!-- 标题 -->

<!-- 按钮组           -->
          <el-row>
              <el-col :span="18">
                    <el-form :inline="true" class="demo-form-inline">
                        <el-form-item label="产品名称">
                            <el-input  placeholder="请输入产品名称查询" v-model="param.name" clearable></el-input>
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
        <el-table :data="products.list" style="width: 100%;">
            <el-table-column prop="id" label="#"></el-table-column>
            <el-table-column prop="name" label="产品名称" ></el-table-column>
            <el-table-column prop="description" label="产品描述" ></el-table-column>
            <el-table-column prop="price" label="价格" ></el-table-column>
            <el-table-column prop="status" label="状态" ></el-table-column>
            <el-table-column prop="photo" label="实物图" >
                <template #default="record">
                    <div id="productPhoto"><img :src="record.row.photo" style="width:100%;height:100%;"></div>
                </template>
            </el-table-column>
            <el-table-column prop="category.name" label="栏目号" ></el-table-column>

            <el-table-column label="操作" >
                <template slot-scope="scope">
                    <el-button type="primary" @click="updateHandler(scope.row)">修改</el-button>
                    <el-button type="danger" @click.prevent="deleteHandler(scope.$index,scope.row.id)">删除</el-button>    
                </template>
            </el-table-column>
        </el-table>
<!-- 表格 -->
<!-- 分页 -->
        <el-pagination
            layout="prev, pager, next"
            :current-page="param.page+1"
            :page-size="param.pageSize"
            :total="products.total"
            @current-change="pageChange">
        </el-pagination>
<!-- 分页 -->
<!-- 模态框 -->
        <el-dialog title="添加产品信息" :visible.sync="dialogFormVisible">
        <el-form :model="product">
            <el-form-item label="产品名称" :label-width="formLabelWidth">
                <el-input v-model="product.name" autocomplete="off" placeholder="请输入产品名称"></el-input>
            </el-form-item>
            <el-form-item label="产品描述" :label-width="formLabelWidth">
                <el-input v-model="product.description" autocomplete="off" placeholder="请输入该产品的描述"></el-input>
            </el-form-item>
            <el-form-item label="价格" :label-width="formLabelWidth">
                <el-input v-model="product.price" autocomplete="off" placeholder="请输入价格"></el-input>
            </el-form-item>
            <el-form-item label="状态" :label-width="formLabelWidth">
                <el-input v-model="product.status" autocomplete="off" placeholder="请输入状态"></el-input>
            </el-form-item>
            <el-form-item label="实物图" :label-width="formLabelWidth">
                <el-input v-model="product.photo" autocomplete="off" placeholder="请输入图片的url"></el-input>
            </el-form-item>
            <el-form-item label="栏目号" :label-width="formLabelWidth">
                <!-- <el-input v-model="product.categoryId" autocomplete="off"></el-input> -->
                <el-select v-model="product.categoryId" placeholder="请选择栏目号" >
                    <el-option v-for="(item, index) in categorys" :key="index" :label="item.name" :value="item.id"></el-option>
                </el-select>
            </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitHandler(product)">提 交</el-button>
        </div>
        </el-dialog>
<!-- 模态框 -->

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
            message:"产品管理",
            products:{
                list:[]
            },
            product:{},
            dialogFormVisible: false,
            formLabelWidth: '120px',
            categorys:[],
            param:{
                name:"",
                page:0,
                pageSize:6
            }
        }
    },
    created(){
        this.reloadData();
        this.selectCategoryName()
    },
    methods:{
        pageChange(page){
            this.param.page = page-1;
            this.reloadData();
        },
        searchByName(){
            this.reloadData();
        },
        async selectCategoryName(){
            let response = await axios.get("/category/findAll").then((response)=>{
                this.categorys=response.data.data
            })
        },
        openDialog(){
            this.product={}
            this.dialogFormVisible = true;
        },
        async reloadData(){
            let response = await axios.post("/product/queryProductCascadeCategory",this.param)
                this.products=response.data.data;
        },
        updateHandler(row){
            this.dialogFormVisible = true;
            this.product=row;            
        },
        async submitHandler(product){
            let data = this.product
            let response = await axios.post("/product/saveOrUpdate",data);
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
            let response = await axios.get("/product/deleteById",{params:{id:id}});
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
    #productPhoto{
        width: 80px;
        height: 80px;
    }
    .el-pagination {
        position: absolute;
        left: 40%;
    }
</style>