<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>商品管理</el-breadcrumb-item>
            <el-breadcrumb-item>商品列表</el-breadcrumb-item>

        </el-breadcrumb>

        <!-- 卡片 -->
        <el-card class="m10">

            <el-row :gutter='16'>
                <el-col :span="8">
                    <el-input
                        placeholder="请输入内容"
                        v-model="user.query"
                        class="input-with-select"
                        clearable
                        @clear="seach"
                        @input="seach"
                    >

                        <el-button
                            slot="append"
                            icon="el-icon-search"
                            @click="seach"
                        ></el-button>
                    </el-input>
                </el-col>
                <el-col :span="16">
                    <el-button type="primary" @click="tianAdd">添加商品</el-button>
                </el-col>
            </el-row>

            <!-- 表格 -->
            <el-table
                :data="list"
                border
                class="m10"
                style="width: 100%"
            >
                <el-table-column
                    prop="date"
                    label="#"
                    width="60"
                    type="index"
                >
                </el-table-column>
                <el-table-column
                    prop="goods_name"
                    label="商品名称"
                    width="480"
                >
                </el-table-column>
                <el-table-column
                    prop="goods_price"
                    label="商品价格"
                >
                </el-table-column>
                <el-table-column
                    prop="goods_weight"
                    label="商品重量"
                >
                </el-table-column>
                <el-table-column
                  
                    label="创建时间"
                >
                <template slot-scope="scope">
                    <div>
                        {{scope.row.add_time | dateTime}}
                    </div>
                </template>
                </el-table-column>
                <el-table-column
                    prop="address"
                    label="操作"
                >
                    <template slot-scope="scope">
                        <el-button
                            type="primary"
                            size="mini"
                        >修改</el-button>
                        <el-button
                            type="danger"
                            size="mini"
                            @click="del(scope.row.goods_id)"
                        >删除</el-button>
                    </template>
                </el-table-column>
            </el-table>

            <!-- 分页 -->
            <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="user.pagenum"
                :page-sizes="[10, 20, 30, 40]"
                :page-size="user.pagesize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total"
            >
            </el-pagination>

        </el-card>

    </div>
</template>

<script>
import { goodListApi ,goodsDelApi} from "../http/api.js";
export default {
  data() {
    return {
      list: [],
      user: {
        query: "",
        pagenum: 1,
        pagesize: 10
      },
      total: 0
    };
  },
  created() {
    this.getlist();
  },
  methods: {
    async getlist() {
        let res =await goodListApi(this.user)
        console.log(res);
        this.list=res.goods
        this.total=res.total
    },
    handleSizeChange(val) {
        this.user.pagesize=val
        this.getlist()
    },
    handleCurrentChange(val) {
        this.user.pagenum=val
        this.getlist()
    },
    seach(){
        this.getlist()
    },
   async del(id){
       let res = await goodsDelApi(id)
       this.getlist()
    },
    //点击按钮跳转到添加商品页
    tianAdd(){
        this.$router.push('/addgoods')
    }
  }
};
</script>

<style scoped >
</style>
