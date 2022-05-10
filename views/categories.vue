<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>商品管理</el-breadcrumb-item>
            <el-breadcrumb-item>商品分类</el-breadcrumb-item>
        </el-breadcrumb>
        <!--  -->
        <el-card class="m10">
            <el-button
                type="primary"
                @click="addkai"
            >添加分类</el-button>

            <!-- //表格 -->
            <el-table
                :data="list"
                border
                style="width: 100%"
                class="m10"
                row-key="cat_id"
            >
                <el-table-column
                    prop="date"
                    label="#"
                    width="180"
                    type="index"
                >
                    <template slot-scope="scope">
                        <div>{{scope.row.index}}</div>
                    </template>
                </el-table-column>
                <el-table-column
                    prop="cat_name"
                    label="分类名称"
                    width="180"
                >
                </el-table-column>
                <el-table-column label="是否有效">
                    <template slot-scope="scope">
                        <i
                            class="el-icon-circle-check"
                            v-show="scope.row.cat_deleted==true"
                        ></i>
                        <i
                            class="el-icon-circle-close"
                            v-show="scope.row.cat_deleted==false"
                        ></i>
                    </template>
                </el-table-column>
                <el-table-column
                    prop=""
                    label="示例"
                >
                    <template slot-scope="scope">
                        <el-tag v-show="scope.row.cat_level==0">一级</el-tag>
                        <el-tag
                            type="success"
                            v-show="scope.row.cat_level==1"
                        >二级</el-tag>
                        <el-tag
                            type="warning"
                            v-show="scope.row.cat_level==2"
                        >三级</el-tag>
                    </template>
                </el-table-column>
                <el-table-column
                    prop="address"
                    label="操作"
                >
                    <template slot-scope="scope">
                        <div>
                            <el-button
                                type="primary"
                                size="mini"
                                @click="edit(scope.row)"
                            >编辑</el-button>
                            <el-button
                                type="danger"
                                size="mini"
                            >删除</el-button>
                        </div>
                    </template>
                </el-table-column>
            </el-table>

            <!-- 分页 -->
            <el-pagination
                class="m10"
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="user.pagenum"
                :page-sizes="[5, 6, 8, 10,12]"
                :page-size="user.pagesize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total"
            >
            </el-pagination>
        </el-card>

        <!-- 添加的对话框 -->
        <el-dialog
            title="添加商品分类"
            :visible.sync="addshow"
            @close="clear"
        >
            <el-form :model="cateFrom">
                <el-form-item
                    label="分类名称"
                    :label-width="formLabelWidth"
                >
                    <el-input
                        autocomplete="off"
                        v-model="cateFrom.cat_name"
                    ></el-input>
                </el-form-item>
                <el-form-item
                    label="父级分类"
                    :label-width="formLabelWidth"
                >
                    <!-- 级联选择器 -->
                    <!-- //商品分类 -->
                    <!-- v-model 当前选中数组的级别-->
                    <!-- :options 级联选择框数据源-->
                    <!--props下拉框的默认配置项  -->
                    <!-- change监听分类改变时触发 -->
                    <el-cascader
                        ref="twoCateRef"
                        v-model="thisCheck"
                        :options="twoCate"
                        :props="defaultProps"
                        @change="handleChange"
                    ></el-cascader>

                </el-form-item>
            </el-form>
            <div
                slot="footer"
                class="dialog-footer"
            >
                <el-button @click=" addshow= false">取 消</el-button>
                <el-button
                    type="primary"
                    @click="addcateOk"
                >确 定</el-button>
            </div>
        </el-dialog>

        <!-- 编辑的对话框 -->
        <el-dialog
            title="修改分类"
            :visible.sync="editShow"
            @close="clear"
        >
            <el-form :model="edcateFrom">
                <el-form-item
                    label="分类名称"
                    :label-width="formLabelWidth"
                >
                    <el-input
                        autocomplete="off"
                        v-model="edcateFrom.cat_name"
                    ></el-input>
                </el-form-item>

            </el-form>
            <div
                slot="footer"
                class="dialog-footer"
            >
                <el-button @click="editShow= false">取 消</el-button>
                <el-button
                    type="primary"
                    @click="addEdit"
                >确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
import { getGoodsCateList, addGoodsCate, addCateEdit } from "../http/api.js";
export default {
  data() {
    return {
      list: [],
      user: {
        type: 3,
        pagenum: 1,
        pagesize: 5
      },
      //编辑的数据
      edcateFrom: {
        cat_name: "",
        cat_id: ""
      },
      total: 0,
      addshow: false, //控制添加的对话框
      editShow: false, //控制编辑的对话框
      formLabelWidth: "100px",
      //绑定分类数据,父级分类的长度=级联长度-1
      cateFrom: {
        cat_name: "",
        cat_pid: "",
        cat_level: ""
      },
      //级联默认配置项
      defaultProps: {
        expandTrigger: "hover",
        label: "cat_name",
        children: "children",
        value: "cat_id",
        checkStrictly: true //可以任意选
      },
      twoCate: [], //级联分类数据
      thisCheck: [] //当前选中的分类等级
    };
  },
  created() {
    this.getlist();
  },
  methods: {
    async getlist() {
      let res = await getGoodsCateList(this.user);
      console.log(res);
      res.result.forEach((item, index) => {
        item.index = index + 1;
      });
      this.list = res.result;
      this.total = res.total;
    },
    handleSizeChange(val) {
      this.user.pagesize = val;
      this.getlist();
    },
    handleCurrentChange(val) {
      this.user.pagenum = val;
      this.getlist();
    },
    //打开添加模态框,调接口立即获取数据，赋给级联选择器
    async addkai() {
      //调分类列表接口,
      let res = await getGoodsCateList({ type: 2 });
      console.log(res);
      this.twoCate = res;
      console.log(this.$refs.twoCateRef); //获取当前的dom元素
      this.addshow = true;
    },
    // 监听分类改变时触发的方法。
    handleChange() {
      this.$refs.twoCateRef.dropDownVisible = false;
    },
    //点击确定添加数据,渲染页面
    async addcateOk() {
      if (this.cateFrom.cat_name == "") {
        this.$message.error("父级名称不能为空");
        return false;
      }
      
      
      if (this.thisCheck.length !== 0) {
        this.cateFrom.cat_pid = this.thisCheck[this.thisCheck.length - 1];
        this.cateFrom.cat_level = this.thisCheck.length;
      } else {
        this.cateFrom.cat_pid = 0;
        this.cateFrom.cat_level = 0;
      }
      //调接口
      let res = await addGoodsCate(this.cateFrom);
       this.addshow = false;
      this.getlist();
     
    },
    //清空模态框数据
    clear() {
      this.cateFrom = {};
      this.thisCheck = [];
    },
    //打开编辑对话框,回填数据
    edit(row) {
      this.editShow = true;
      this.edcateFrom.cat_name = row.cat_name;
      this.edcateFrom.cat_id = row.cat_id;
    },
    //点击确定,提交修改数据
    async addEdit() {
      console.log(this.edcateFrom);
      let cat_id = this.edcateFrom.cat_id;
      let cat_name = this.edcateFrom.cat_name;
      let res = await addCateEdit(cat_id, { cat_name });
      this.getlist();
      this.editShow = false;
    }
  }
};
</script>
<style>
.el-scrollbar__view {
  height: 300px;
}
.el-radio {
  position: absolute;
  width: 100%;
  height: 25px;
}
.el-radio__inner {
  display: none;
}
</style>


<style scoped lang="scss">
.el-icon-circle-check {
  color: greenyellow;
}
.el-icon-circle-close {
  color: red;
}
// .el-cascader{
//     width: 100%;
// }
</style>
