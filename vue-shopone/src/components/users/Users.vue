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
          <el-button type="primary" @click="addClick">添加用户</el-button>
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
            <el-button type="primary" icon="el-icon-edit" @click="editClick(scope.row.id)"></el-button>
            <el-button type="primary" icon="el-icon-delete"></el-button>
            <el-tooltip effect="dark" content="设置" placement="top" :enterable="false">
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
        :total="total"
      ></el-pagination>
    </el-card>
    <!-- 添加对话框区域 -->
    <el-dialog title="添加用户" :visible.sync="addDialogVisible" width="50%" @close="closeDialog">
      <!--内容主体区-->
      <el-form
        :model="addRuleForm"
        :rules="addRules"
        ref="addruleForm"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="用户名" prop="username">
          <el-input v-model="addRuleForm.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="addRuleForm.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="addRuleForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input v-model="addRuleForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <!-- 底部 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addUser">确 定</el-button>
      </span>
    </el-dialog>
    <!-- 编辑对话框 -->
    <el-dialog title="编辑用户" :visible.sync="editdialogVisible" width="50%" close="editClose">
      <el-form :model="editForm" :rules="editrules" ref="editruleForm" label-width="100px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="editForm.username" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="editForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机" prop="mobile">
          <el-input v-model="editForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editdialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="editDioClick">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import axios from "axios";
export default {
  components: {},
  data() {
    return {
      editrules: {
        username: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          { min: 3, max: 50, message: "长度在 3 到 50 个字符", trigger: "blur" }
        ],
        email: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          { min: 3, max: 50, message: "长度在 3 到 50 个字符", trigger: "blur" }
        ],
        mobile: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          { min: 3, max: 50, message: "长度在 3 到 50 个字符", trigger: "blur" }
        ],
      },
      editForm: {},
      editdialogVisible: false,
      queryInfo: {
        query: "",
        //当前的页数
        pagenum: 1,
        //每页显示多少条数据
        pagesize: 2
      },
      userlist: [],
      total: 0,
      switchdata: "",
      addDialogVisible: false,
      addRuleForm: {
        username: "",
        password: "",
        email: "",
        mobile: ""
      },
      addRules: {
        username: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          { min: 3, max: 50, message: "长度在 3 到 50 个字符", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          { min: 3, max: 50, message: "长度在 3 到 50 个字符", trigger: "blur" }
        ],
        email: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          { min: 3, max: 50, message: "长度在 3 到 50 个字符", trigger: "blur" }
        ],
        mobile: [
          { required: true, message: "请输入活动名称", trigger: "blur" },
          { min: 3, max: 50, message: "长度在 3 到 50 个字符", trigger: "blur" }
        ]
      }
    };
  },
  created() {
    this.getUsersList();
  },
  mounted() {},
  watch: {},
  computed: {},
  methods: {
    editDioClick(){
      this.$refs.editruleForm.validate(valid=>{
        if(!valid) return 
        //发起网络请求
        axios.put('http://timemeetyou.com:8889/api/private/v1/users/'+this.editForm.id,{email:this.editForm.email,mobile:this.editForm.mobile}).then(res=>{
          if(res.data.meta.status!=200){
            return this.$message.error('更改失败')
          }
          this.$message.success('更改成功')
          this.getUsersList()
          this.editdialogVisible=false
        })
      })
    },
    editClose(){
      this.$refs.editruleForm.resetFields()
    },
    editClick(id) {
      axios
        .get("http://timemeetyou.com:8889/api/private/v1/users/" + id)
        .then(res => {
          if (res.data.meta.status != 200) {
            return this.$message.error("编辑错误");
          }
          this.editForm = res.data.data;
          this.editdialogVisible = true;
        });
    },
    addUser() {
      this.$refs.addruleForm.validate(valid => {
        if (!valid) return;
        //发起真正的网络请求
        axios
          .post(
            "http://timemeetyou.com:8889/api/private/v1/users",
            this.addRuleForm
          )
          .then(res => {
            if (res != 201) {
              this.$message.error("添加用户失败");
            }
            this.$message.success("添加用户成功");
            this.addDialogVisible = false;
            this.getUsersList();
          });
      });
    },
    closeDialog() {
      this.$refs.addruleForm.resetFields();
    },
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
    handleSizeChange(pageSize) {
      this.queryInfo.pagesize = pageSize;
      this.getUsersList();
    },
    handleCurrentChange(pageCurrent) {
      this.queryInfo.pagenum = pageCurrent;
      this.getUsersList();
    },
    //添加按钮点击事件
    addClick() {
      this.addDialogVisible = true;
    },
    //保存数据
    userStateChanged(userinfo) {
      console.log(userinfo);
      axios
        .put(
          `http://timemeetyou.com:8889/api/private/v1/users/${userinfo.id}/state/${userinfo.mg_state}`
        )
        .then(res => {
          console.log(res);
          this.switchdata = res.data;
          if (this.switchdata.meta.status !== 200) {
            console.log(5);
            userinfo.mg_stata = !userinfo.mg_stata;
            return this.$message.error("更新用户状态失败");
          }
          this.$message.success("更新用户状态成功");
        });
    }
  }
};
</script>
<style scoped>
</style>