<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>权限管理</el-breadcrumb-item>
            <el-breadcrumb-item>权限列表</el-breadcrumb-item>
        </el-breadcrumb>

        <el-card class="m10">
            <el-table
                :data="list"
                border
                style="width: 100%"
            >
                <el-table-column
                    type="index"
                    label="#"
                    width="60"
                >
                </el-table-column>
                <el-table-column
                    prop="authName"
                    label="职称"
                  
                >
                </el-table-column>
                <el-table-column
                    prop="path"
                    label="路径"
                >
                </el-table-column>
                 <el-table-column
                    prop="level"
                    label="权限等级"
                    
                >
                    <template slot-scope="scope">
                        <el-tag v-show="scope.row.level==0">一级</el-tag>
                        <el-tag type="success" v-show="scope.row.level==1">二级</el-tag>
                        <el-tag type="warning" v-show="scope.row.level==2">三级</el-tag>
                    </template>

                </el-table-column>
            
            </el-table>
        </el-card>
    </div>
</template>

<script>
import {qxianlistApi} from '../http/api.js'
export default {
  data() {
    return {
        list:[],//列表数据
        
    };
  },

  created() {
      this.getlist()
  },
  methods: {
      async getlist(){
          let res=await qxianlistApi()
        //   console.log(res);
          this.list=res
      }
  }
};
</script>

<style scoped >
</style>
