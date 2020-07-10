<template>
  <el-container>
    <el-header>
      <div>
        <img src="~assets/123.jpg" alt />
        <span>电商后台管理系统</span>
      </div>
      <el-button type="info" @click="tokenout">退出</el-button>
    </el-header>
    <el-container>
      <el-aside :width="iscollap?'64px':'200px'">
        <!-- 折叠按钮 -->
        <div class="zhediebutton" @click="zhedieClick">|||</div>
        <!-- 一级菜单 -->
        <el-menu background-color="#333744" text-color="#fff" active-text-color="#409eff" unique-opened :collapse="iscollap"
          :collapse-transition='false'
          :router="true"
          :default-active='activepath'>
          <el-submenu :index="item.id+''" v-for="item in menulist" :key='item.id'>
            <template slot="title">
              <i :class="menuIcons[item.id]"></i>
              <span>{{item.authName}}</span>
            </template>
            <!-- 二级菜单 -->
            <el-menu-item :index="'/'+menuItem.path" v-for="menuItem in item.children" :key="menuItem.id"
              @click="menuItemClick('/'+menuItem.path)">
              <i class="el-icon-menu"></i>
              <span>{{menuItem.authName}}</span>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import axios from 'axios'
export default {
  components: {},
  data() {
    return {
      menulist:[],
      menuIcons:{
        '125':'iconfont icon-user',
        '103':'iconfont icon-quanxian',
        '101':'iconfont icon-shangpinguanli',
        '102':'iconfont icon-dingdanguanli',
        '145':'iconfont icon-shujutongji',
      },
      iscollap:false,
      activepath:''
    };
  },
  created() {
    this.getMenuList(),
    this.activepath=window.sessionStorage.getItem('menupath')
  },
  mounted() {},
  watch: {},
  computed: {},
  methods: {
    tokenout() {
      window.sessionStorage.clear();
      this.$router.push("/login");
    },
    getMenuList(){
      axios.get('http://timemeetyou.com:8889/api/private/v1/menus').then(res=>{
        if(res.data.meta.status!=200) return this.$message.error(res.data.meta.msg)
        this.menulist=res.data.data
        console.log(res.data.data)
      })
    },
    zhedieClick(){
      this.iscollap=!this.iscollap
    },
    menuItemClick(menupath){
      window.sessionStorage.setItem('menupath',menupath)
      // this.activepath=menupath
    }
  }
};
</script>
<style scoped>
.el-header {
  background-color: #373d41;
  display: flex;
  justify-content: space-between;
  padding-left: 0;
  align-items: center;
  color: #fff;
  font-size: 20px;
}
.el-header div {
  display: flex;
  align-items: center;
}
.el-header div span {
  margin-left: 15px;
}
.el-aside {
  background-color: #333744;
}
.el-main {
  background-color: #eaedf1;
}
.el-container {
  width: 100%;
  height: 100%;
}
.el-button {
  margin-left: 500px;
}
.iconfont{
  margin-right: 10px;
}
.el-menu{
  border-right: 0;
}
.zhediebutton{
  background-color: #475163;
  text-align: center;
  line-height: 20px;
  color: #fff;
  letter-spacing:2px;
}
</style>