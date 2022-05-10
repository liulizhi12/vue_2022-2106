<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>订单管理</el-breadcrumb-item>
            <el-breadcrumb-item>订单列表</el-breadcrumb-item>
        </el-breadcrumb>
        <!--  -->
        <el-card class="m10">
            <el-row>
                <el-col :span="8">
                    <el-input
                        placeholder="请输入内容"
                        class="input-with-select"
                    >

                        <el-button
                            slot="append"
                            icon="el-icon-search"
                        ></el-button>
                    </el-input>
                </el-col>
            </el-row>
            <!-- 表格区 -->
            <el-table
                :data="list"
                class="m10"
                style="width: 100%"
            >
                <el-table-column
                    label="#"
                    width="60"
                    type="index"
                >
                </el-table-column>
                <el-table-column
                    prop="order_number"
                    label="订单编号"
                    width="260"
                >
                </el-table-column>
                <el-table-column
                    prop="order_price"
                    label="订单价格"
                >
                </el-table-column>
                <el-table-column
                    prop="address"
                    label="是否付款"
                >
                    <template slot-scope="scope">
                        <div>
                            <el-tag type="success" size="mini" v-show="scope.row.pay_status==1">已付款</el-tag>
                            <el-tag type="danger" size="mini" v-show="scope.row.pay_status==0">未付款</el-tag>
                        </div>
                    </template>

                </el-table-column>
                <el-table-column
                    prop="is_send"
                    label="是否发货"
                >
                </el-table-column>
                <el-table-column
                    prop="address"
                    label="下单时间"
                      width="260"
                >
                <template slot-scope="scope">
                    <div>
                        {{scope.row.create_time | dateTime}}
                    </div>
                </template>
                </el-table-column>
                <el-table-column
                    prop="address"
                    label="操作"
                >
                <template slot-scope="scope">
                    <div>
                         <el-button type="primary" icon="el-icon-edit" circle size="mini"></el-button>
                           <el-button type="info" icon="el-icon-location" size="mini" circle></el-button>
                    </div>
                </template>
                </el-table-column>
            </el-table>

            <!-- 分页区 -->
            <el-pagination
                class="m10"
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="from.pagenum"
                :page-sizes="[4, 6, 8, 10]"
                :page-size="from.pagesize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total"
            >
            </el-pagination>
        </el-card>
    </div>
</template>

<script>
import { getOrdersListApi } from "../http/api.js";
export default {
  data() {
    return {
      list: [],
      from: {
        query: "",
        pagenum: 1,
        pagesize: 6
      },
      total: 0
    };
  },
  created() {
    this.getlist();
  },
  methods: {
    async getlist() {
      let res = await getOrdersListApi(this.from);
      console.log(res);
      this.list = res.goods;
      this.total = res.total;
    },
    handleSizeChange(val) {
      this.from.pagesize = val;
      this.getlist();
    },
    handleCurrentChange(val) {
      this.from.pagenum = val;
      this.getlist();
    }
  }
};
</script>

<style scoped >
</style>
