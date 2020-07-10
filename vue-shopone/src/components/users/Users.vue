<template>
  <div class>
    <!-- 面包靴导航区 -->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片区域 -->
    <el-card>
      <el-row :gutter="50">
        <el-col :span="8">
          <el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getUsersList">
            <el-button @click="getUsersList" slot="append" icon="el-icon-search"></el-button>
          </el-input>
        </el-col>
        <el-col :span="3">
          <el-button type="primary">添加用户</el-button>
        </el-col>
      </el-row>
      <!-- 表格制作 -->
      <el-table :data="userlist" border style="width: 100%">
        <el-table-column type="index"></el-table-column>
        <el-table-column prop="username" label="姓名"></el-table-column>
        <el-table-column prop="email" label="邮箱"></el-table-column>
        <el-table-column prop="mobile" label="电话"></el-table-column>
        <el-table-column prop="role_name" label="角色"></el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">
            <el-switch v-model="scope.row.mg_state" @change="userStateChanged(scope.row)"></el-switch>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button type="primary" icon="el-icon-edit"></el-button>
            <el-button type="primary" icon="el-icon-delete"></el-button>
            <el-tooltip effect="dark" content="设置" placement="top" :enterable='false'>
              <el-button type="primary" icon="el-icon-share"></el-button>
            </el-tooltip>          
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页区域 -->
      <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="queryInfo.pagenum"
      :page-sizes="[2, 3, 10]"
      :page-size="queryInfo.pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
    </el-card>
  </div>
</template>

<script>
import axios from "axios";
export default {
  components: {},
  data() {
    return {
      queryInfo: {
        query: "",
        //当前的页数
        pagenum: 1,
        //每页显示多少条数据
        pagesize: 2
      },
      userlist: [],
      total: 0,
      switchdata:''
    };
  },
  created() {
    this.getUsersList();
  },
  mounted() {},
  watch: {},
  computed: {},
  methods: {
    getUsersList() {
      axios("http://timemeetyou.com:8889/api/private/v1/users", {
        params: this.queryInfo
      }).then(res => {
        if (res.data.meta.status != 200) {
          return this.$message.error("获取用户列表错误");
        }
        this.userlist = res.data.data.users;
        this.total = res.data.data.total;
        console.log(res);
      });
    },
    handleSizeChange(pageSize){
      this.queryInfo.pagesize=pageSize
      this.getUsersList()
    },
    handleCurrentChange(pageCurrent){
      this.queryInfo.pagenum=pageCurrent
      this.getUsersList()
    },
    //保存数据
    userStateChanged(userinfo){
      console.log(userinfo)
      axios.put(`http://timemeetyou.com:8889/api/private/v1/users/${userinfo.id}/state/${userinfo.mg_state}`).then(res=>{
        console.log(res)
        this.switchdata=res.data
        if(this.switchdata.meta.status!==200){
        console.log(5)
        userinfo.mg_stata=!userinfo.mg_stata
        return this.$message.error('更新用户状态失败')
      }
      this.$message.success('更新用户状态成功')
      })
    }
  }
};
</script>
<style scoped>
</style>