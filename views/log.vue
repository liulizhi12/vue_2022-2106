<template>
  <div class="loghome">
    <div class="login">
      <h2>电商后台管理系统</h2>
      <el-form ref="loginRef" :model="loginFrom" :rules="loginFromval">
          <el-form-item prop="username">
            <el-input placeholder="请输入用户名" v-model="loginFrom.username" prefix-icon="iconfont icon-user"></el-input>
          </el-form-item>
     
          <el-form-item prop="password">
            <el-input placeholder="请输入密码" v-model="loginFrom.password" prefix-icon="iconfont icon-password" type="password" show-password></el-input>
          </el-form-item>
          <el-form-item class="elformitem">
              <el-button type="primary" @click="tohome">登录</el-button>
              <el-button type="info">重置</el-button>
          </el-form-item>
          
      </el-form>
    </div>
  </div>
</template>

<script>
import {login} from "../http/api.js"
import { log } from 'util';
export default {
  data() {
    return {
      // 表单绑定的元素
      loginFrom: {
        username: "admin",
        password: "123456",
      },
      //   表单验证属性
      loginFromval: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" },
          {
            min: 2,
            max: 8,
            message: "输入的用户长度必需要5~20位之间",
            trigger: "blur",
          },
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          //字符长度处理
          {
            min: 5,
            max: 20,
            message: "输入的密码长度必需要5~20位之间",
            trigger: "blur",
          },
        ],
      },
    };
  },
  methods: {
    tohome() {
      // this.$refs.loginRef.validate(async (valid) => {
      //   // console.log("valid:", valid);
      //   if (!valid) return false;
      //   this.res = await LoginApi(this.loginFrom);
      //   // console.log("是否登录成功：", res);
      //   this.$router.push("/home");
      // });
      this.$refs.loginRef.validate(async valid=>{
        // console.log(valid);
        if(!valid){
          return false
        }else{
          // console.log("ok");
          //开始调用接口
          let res=await login(this.loginFrom)
          // console.log(res);
          this.$router.push('/home')
        }
        
      })
    },
  },
  components: {},
};
</script>

<style lang='scss'>
.loghome {
  width: 100%;
  height: 100%;
  background: #1e1e1e;
  display: flex;
  justify-content: center;
  align-items: center;
  .login {
    width: 500px;
    height: 250px;
    background: #fff;
    padding: 20px;
    border-radius: 5px;
    h2 {
      text-align: center;
    }
    .elformitem {
      display: flex;
      justify-content: flex-end;
    }
  }
}
</style>
