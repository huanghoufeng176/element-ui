<template>
  <div class="login-box">
    <div class="login-dneglu">
      <!-- 头像区域 -->
      <div class="login-img">
        <img src="~assets/logo.png" alt="">
      </div>
      <!-- 登录表单区 -->
      <el-form ref="formLoginReset" class="login-form" :model="form" :rules="rules">
        <el-form-item prop="username">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input v-model="form.password" type="password"></el-input>
        </el-form-item>
        <el-form-item class="login-button">
          <el-button type="primary" @click="denglu">登录</el-button>
          <el-button type="info" @click="loginReset">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
// import {request} from '../../network/request'
import axios from 'axios'
// v-model="form.username"   v-model="form.password"
export default {
  components:{},
  data(){
    return {
      dengludata:'',
      form:{
        username:'admin',
        password:'123456'
      },
      rules:{
        username: [
            { required: true, message: '请输入用户名', trigger: 'blur' },
            { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ],
        password: [
            { required: true, message: '请输入活动名称', trigger: 'blur' },
            { min: 6, max: 12, message: '长度在 6 到 12 个字符', trigger: 'blur' }
          ],
      }
    }
  },
  created(){},
  mounted(){},
  watch:{},
  computed:{},
  methods:{
    loginReset(){
      this.$refs.formLoginReset.resetFields()
    },
    denglu(){
      this.$refs.formLoginReset.validate(valid=>{
        if(!valid) return;
        axios.post('http://timemeetyou.com:8889/api/private/v1/login',this.form).then(res=>{
          console.log(res.data)
          this.dengludata=res.data;
          if(res.data.meta.status!==200) return this.$message.error('登录失败')
          this.$message.success('登录成功')
          //1.将登录成功之后的token，保存到客户端的sessionstorage中
          //  1.1项目中出了登录之外的其他api接口，必须在登录之后才能访问
          //  1.2token 只应在当前网站打开期间生效，所以将token保存在sessionstorage中
          window.sessionStorage.setItem('token',res.data.data.token)
          this.$router.push('/home')
        })
      })
    }
  },
}
</script>
<style scoped>
  .login-box{
    height: 100%;
    background-color: #2b4b6b;
  }
  .login-dneglu{
    width: 400px;
    height: 250px;
    background-color: #fff;
    position: absolute;
    left: 50%;
    top: 50%;
    margin-left: -200px;
    margin-top: -125px;
  }
  .login-img{
    width: 130px;
    height: 130px;
    border: 1px solid #eee;
    border-radius: 50%;
    padding: 10px;
    position: absolute;
    left: 50%;
    top: -70px;
    margin-left: -75px;
    background-color: #fff;
  }
  .login-img img{
    width: 100%;
    height: 100%px;
    border: 1px solid #ddd;
    border-radius: 50%;
    background-color: #ddd;
  }
  .login-button{
    margin-left: 220px;
  }
  .login-form{
    margin-top: 70px;
    padding: 0 10px;
  }
</style>