<template>
  <div class="box">
    <el-container>
      <el-header>
        <el-row>
          <el-col :span="2">
            <!-- <h2> logo</h2> -->
            <img src="../assets/屏幕截图 2022-04-26 092506.jpg" alt="" class="img">
            <!-- <div class="ts">电商后台管理系统</div> -->
          </el-col>
          <el-col :span="20">
            <div class="ts">电商后台管理系统</div>
          </el-col>
          <el-col :span="2">
            <el-button type="info" @click="gotout">退出</el-button>
          </el-col>
        </el-row>
      </el-header>
      <el-container>
        <!-- //菜单导航 -->
        <el-aside :width="isshow?'65px':'200px'">
          <div class="til" @click="isshow=!isshow">|||</div>
          <el-menu
            :default-active="$route.path"
            class="el-menu-vertical-demo"
            background-color="rgb(51, 55, 68)"
            text-color="#fff"
            active-text-color="#ffd04b"
            :unique-opened="true"
            :router="true"
            :collapse="isshow"
            :collapse-transition="false"
          >
            <el-submenu :index="item.id+''" v-for="item in menus" :key="item.id">
              <template slot="title">
                <i class="el-icon-user"></i>
                <span>{{item.authName}}</span>
              </template>

              <el-menu-item :index="'/'+item2.path" v-for="item2 in item.children" :key="item2.id">
                <i class="el-icon-menu"></i>
                <span slot="title">{{item2.authName}}</span>
              </el-menu-item>
            </el-submenu>

          </el-menu>
        </el-aside>
        <!-- 主体 -->
        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>

  </div>
</template>

<script>
import {getnavApi} from "../http/api.js"
export default {
  data() {
    return {
      menus:[],
      isshow:false,//左侧菜单
    };
  },
  created() {
    this.getnav()
  },
  methods: {
    async getnav(){
      let res = await getnavApi()
      // console.log(res);
      this.menus=res
    },
    //退出的操作
    gotout(){
      sessionStorage.removeItem('token')
      this.$router.replace('/log')
    }
  },
  components: {}
};
</script>

<style lang='scss'>
.box {
  width: 100%;
  height: 100%;
}
.el-header,
.el-footer {
  color: #333;
  text-align: center;
  line-height: 60px;
}
.el-header {
  background-color: rgb(55, 61, 65);
  // h2 {
  //   padding: 0;
  //   margin: 0;
  //   color: rgb(245, 127, 48);
  // }
  .img{
    width: 57px;
    height: 50px;
    margin-top: 6px;
  }
  .ts {
    font-size: 18px;
    font-weight: 600;
    color: #fff;
  }
}

.el-aside {
  background-color: rgb(51, 55, 68);
  color: #333;
  height: 100%;
  // line-height: 200px;
  height: 100%;
  .til{
    padding: 8px 0;
    box-sizing: border-box;
    background: #4a5064;
    color: white;
    text-align: center;
  }
}

.el-main {
  background-color: #e9eef3;
  color: #333;
 
 
  
}
.el-container {
  height: 100%;
}
.el-menu{
  border-right: none !important;
}

</style>
