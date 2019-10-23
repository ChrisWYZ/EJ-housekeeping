<template>
  <div id="app">
    <!-- 品字布局 -->
    <el-container>
      <!-- 品字布局-头部 -->
      <el-header>
        <h1>{{name}}</h1>
      </el-header>
      <!-- 品字布局-头部 -->
      <el-container>
        <!-- 品字布局-左侧导航 -->
        <el-aside width="200px">
          <el-col :span="24">
            <el-menu
              default-active="0"
              background-color="#545c64"
              text-color="#fff"
              active-text-color="#ffd04b" 
              id="el-menu">
              <el-menu-item index="0" @click="current=''">
                <i class="el-icon-menu"></i>
                <span>首页</span>
              </el-menu-item>
              <el-menu-item index="1" @click="current='customer'">
                <i class="el-icon-setting"></i>
                <span>顾客管理</span>
              </el-menu-item>
              <el-menu-item index="2" @click="current='category'">
                <i class="el-icon-setting"></i>
                <span>栏目管理</span>
              </el-menu-item>
              <el-menu-item index="3" @click="current='address1'">
                <i class="el-icon-setting"></i>
                <span>地址管理</span>
              </el-menu-item>
              <el-menu-item index="4" @click="current='product'">
                <i class="el-icon-setting"></i>
                <span>产品管理</span>
              </el-menu-item>
              <el-menu-item index="5" @click="current='comment'">
                <i class="el-icon-setting"></i>
                <span>评论管理</span>
              </el-menu-item>
            </el-menu>
          </el-col>
        </el-aside>
        <!-- 品字布局-左侧导航 -->
        <!-- 品字布局-内容部分 -->
        <el-main>
          <div>
            <component :is="current"></component>
          </div>
        </el-main>
        <!-- 品字布局-内容部分 -->
      </el-container>
    </el-container>
    <!-- 品字布局 -->

    
    <!-- <h1>
      {{message}}
    </h1> -->
  </div>
</template>

<script>
import customer from './pages/Customer.vue'
import category from './pages/Category.vue'
import address1 from './pages/Address.vue'
import comment from './pages/Comment.vue'
import product from './pages/Product.vue'





export default {
  data(){
    return {
      name: '家政服务后台系统',
      // message:"你好Vue，然后，再你妈的见",
      current:"customer"
    }
  },
  components: {
    customer,
    category,
    address1,
    comment,
    product
  }
}
</script>

<style>
  body,html{
    margin: 0;
    padding: 0;
  }
  .el-header{
    background-color: rgb(84, 92, 100);
    border-bottom: .5px solid #fff;
    color: #333;
    text-align: center;
    line-height: 20px;
    position: relative;
    left: 0;
    top: 0;
    right: 0;
    width: 100%;
  }
  
  #el-menu {
    position: absolute;
    left: 0;
    bottom: 0;
    top: 60px;
    width: 208px;
  }
  body > .el-container {
    margin-bottom: 40px;
  }

</style>
