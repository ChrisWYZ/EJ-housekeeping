<template>
    <div id="app">
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>地址管理</el-breadcrumb-item>
        </el-breadcrumb>
        <h1>{{message}}</h1>
        <el-button type="primary" @click="openDialog">添加</el-button>
        {{addresss}}
        <el-table :data="addresss" style="width: 100%;">
            <el-table-column prop="id" label="#"></el-table-column>
            <el-table-column prop="province" label="省份" ></el-table-column>
            <el-table-column prop="city" label="城市" ></el-table-column>
            <el-table-column prop="area" label="地区" ></el-table-column>
            <el-table-column prop="address" label="地址" ></el-table-column>
            <el-table-column prop="telephone" label="联系方式" ></el-table-column>
            <el-table-column prop="customerId" label="顾客ID" ></el-table-column>
            <el-table-column label="操作" >
                <template slot-scope="scope">
                    <el-button type="primary" @click="updateHandler(scope.row)">修改</el-button>
                    <el-button type="danger" @click.prevent="deleteHandler(scope.$index,scope.row.id)">删除</el-button>    
                </template>
            </el-table-column>
        </el-table>



        <el-dialog title="添加地址信息" :visible.sync="dialogFormVisible">
        <el-form :model="address">
            {{customers}}
            <el-form-item label="省份" :label-width="formLabelWidth">
            <el-input v-model="address.province" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="城市" :label-width="formLabelWidth">
            <el-input v-model="address.city" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="地区" :label-width="formLabelWidth">
            <el-input v-model="address.area" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="地址" :label-width="formLabelWidth">
            <el-input v-model="address.address" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="联系方式" :label-width="formLabelWidth">
            <el-input v-model="address.telephone" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="顾客" :label-width="formLabelWidth">
                <el-select v-model="address.customerId" placeholder="请选择顾客" >
                    <el-option v-for="(item, index) in customers" :key="index" :label="item.realname" :value="item.id"></el-option>
                </el-select>
            </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取 消</el-button>
            <el-button type="primary" @click="submitHandler(address)">提 交</el-button>
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
import qs from 'qs'
import axios from 'axios'
export default {
    name:'app',
    data(){
        return{
            message:"地址管理",
            addresss:[],
            param:{
                name:'',
                page:0,
                pageSize:5
            },
            address:{},
            dialogFormVisible: false,
            formLabelWidth: '120px',
            customers:[]
        }
    },
    created(){
        this.reloadData();
        this.selectCustomerName();
    },
    methods:{
        async selectCustomerName(){
            let response = await axios.get("/customer/findAll")
            this.customers = response.data.data
        },
        openDialog(){
            this.address={}
            this.dialogFormVisible = true;
        },
        async reloadData(){
            let response = await axios.get("/address/findAll")
                this.addresss=response.data.data;
        },
        updateHandler(row){
            this.dialogFormVisible = true;
            this.address=row;            
        },
        async submitHandler(address){
            let response = await axios.post("/address/saveOrUpdate",this.address);
            this.dialogFormVisible = false;
            if(response.data.status===200){
                this.$message({
                    type:'success',
                    message:'保存成功'
                });
            }else {
                this.$message({
                    type:"danger",
                    message:'保存失败'
                })
            }
            this.reloadData();

        },
        async deleteHandler(index,id){
            let response = await axios.get("/address/deleteById",{params:{id:id}});
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
    h1 {
        margin: 0;
        padding: 0;
        color:rgb(78, 11, 11);
        text-align: center
    }
</style>