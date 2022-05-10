<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>用户管理</el-breadcrumb-item>
            <el-breadcrumb-item>用户列表</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- 卡片 -->
        <el-card class="m10">
            <!-- //搜索区域 -->
            <el-row :gutter="15">
                <el-col :span="8">
                    <el-input
                        placeholder="请输入内容"
                        class="input-with-select"
                        v-model="queryinfo.query"
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
                <el-col :span="3">
                    <el-button
                        type="primary"
                        @click="add"
                    >添加用户</el-button>
                </el-col>

            </el-row>
            <!-- //表格区域 -->
            <el-table
                :data="list"
                border
                style="width: 100%"
                class="m10"
            >
                <el-table-column
                    label="#"
                    type="index"
                ></el-table-column>
                <el-table-column
                    label="姓名"
                    prop="username"
                ></el-table-column>
                <el-table-column
                    label="邮箱"
                    prop="email"
                ></el-table-column>
                <el-table-column
                    label="电话"
                    prop="mobile"
                ></el-table-column>
                <el-table-column
                    label="角色"
                    prop="role_name"
                ></el-table-column>
                <el-table-column label="状态">
                    <template slot-scope="scope">
                        <div>
                            <el-switch
                                v-model="scope.row.mg_state"
                                @change="changeStatus(scope.row)"
                            >
                            </el-switch>
                        </div>
                    </template>
                </el-table-column>
                <el-table-column label="操作">
                    <template slot-scope="scope">
                        <div>
                            <!-- //编辑的按钮 -->
                            <el-button
                                type="primary"
                                icon="el-icon-edit"
                                size="mini"
                                @click="edDakai(scope.row)"
                            ></el-button>
                            <!-- //删除的按钮 -->
                            <el-button
                                type="danger"
                                icon="el-icon-delete"
                                size="mini"
                                @click="del(scope.row)"
                            ></el-button>
                            <!-- 用户分配角色的按钮 -->
                            <el-button
                                type="warning"
                                icon="el-icon-edit"
                                size="mini"
                                @click="changejse(scope.row)"
                            ></el-button>

                        </div>
                    </template>
                </el-table-column>
            </el-table>

            <!-- //分页 -->
            <el-pagination
                class="m10"
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="queryinfo.pagenum"
                :page-sizes="[2,3, 5, 6, 8]"
                :page-size="queryinfo.pagesize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total"
            >
            </el-pagination>
        </el-card>

        <!-- //添加的模态框 -->
        <el-dialog
            title="收货地址"
            :visible.sync="isshow"
        >
            <el-form
                :model="from"
                :rules="rules"
                ref="ruleForm"
            >
                <el-form-item
                    label="用户名 :"
                    :label-width="formLabelWidth"
                    prop="username"
                >
                    <el-input
                        autocomplete="off"
                        v-model="from.username"
                    ></el-input>
                </el-form-item>
                <el-form-item
                    label="密码 :"
                    :label-width="formLabelWidth"
                    prop="password"
                >
                    <el-input
                        autocomplete="off"
                        v-model="from.password"
                    ></el-input>
                </el-form-item>
                <el-form-item
                    label="邮箱 :"
                    :label-width="formLabelWidth"
                    prop="email"
                >
                    <el-input
                        autocomplete="off"
                        v-model="from.email"
                    ></el-input>
                </el-form-item>
                <el-form-item
                    label="电话 :"
                    :label-width="formLabelWidth"
                    prop="mobile"
                >
                    <el-input
                        autocomplete="off"
                        v-model="from.mobile"
                    ></el-input>
                </el-form-item>

            </el-form>
            <div
                slot="footer"
                class="dialog-footer"
            >
                <el-button>取 消</el-button>
                <el-button
                    type="primary"
                    @click="addok"
                >确 定</el-button>
            </div>
        </el-dialog>

        <!-- //修改的模态框 -->
        <el-dialog
            title="收货地址"
            :visible.sync="isbji"
        >
            <el-form
                :model="from"
                :rules="rules"
                ref="ybanji"
            >
                <el-form-item
                    label="用户名 :"
                    :label-width="formLabelWidth"
                    prop="username"
                >
                    <el-input
                        autocomplete="off"
                        v-model="from.username"
                        disabled
                    ></el-input>
                </el-form-item>

                <el-form-item
                    label="邮箱 :"
                    :label-width="formLabelWidth"
                    prop="email"
                >
                    <el-input
                        autocomplete="off"
                        v-model="from.email"
                    ></el-input>
                </el-form-item>
                <el-form-item
                    label="电话 :"
                    :label-width="formLabelWidth"
                    prop="mobile"
                >
                    <el-input
                        autocomplete="off"
                        v-model="from.mobile"
                    ></el-input>
                </el-form-item>

            </el-form>
            <div
                slot="footer"
                class="dialog-footer"
            >
                <el-button>取 消</el-button>
                <el-button
                    type="primary"
                    @click="edit"
                >确 定</el-button>
            </div>
        </el-dialog>

        <!-- 修改角色的对话框 -->
        <el-dialog
            title="收货地址"
            :visible.sync="showjse"
        >
            <el-form :model="statusfrom">

                <p>当前用户：{{xinjse.username}}</p>

                <p>当前角色：{{xinjse.role_name}}</p>

                <el-form-item label="分配新角色">
                    <el-select
                        v-model="statusfrom.cid"
                        placeholder="请选择活动区域"
                    >
                        <el-option
                            :label="item.roleName"
                            :value="item.id"
                            v-for="item in rolesList"
                            :key="item.id"
                        ></el-option>

                    </el-select>
                </el-form-item>

            </el-form>
            <div
                slot="footer"
                class="dialog-footer"
            >
                <el-button @click="showjse = false">取 消</el-button>
                <el-button
                    type="primary"
                    @click="fpeijse"
                >确 定</el-button>
            </div>
        </el-dialog>

    </div>
</template>

<script>
import {
  getuserApi,
  addApi,
  editApi,
  getdelApi,
  changeStatus,
  getjse,
  getFpei,
} from "../http/api.js";
import _ from "lodash";
export default {
  data() {
    //验证邮箱
    let email = (rule, value, callback) => {
      let pp = /^[A-Za-z0-9\u4e00-\u9fa5]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/;
      if (value.trim().length === 0) {
        return callback(new Error("请输入邮箱"));
      }
      if (!pp.test(value)) {
        return callback(new Error("您输入的邮箱不合法"));
      }

      callback();
    };
    //验证手机号
    let mobile = (rule, value, callback) => {
      let pp = /^1[3|4|5|7|8][0-9]{9}$/;
      if (value.trim().length === 0) {
        return callback(new Error("请输入手机号"));
      }
      if (!pp.test(value)) {
        return callback(new Error("请输入正确的手机号"));
      }
      callback();
    };

    return {
      list: [],
      total: 0,
      showjse: false,
      isshow: false,
      statusfrom: {
          cid:""
      }, //绑定分配角色数据
      xinjse: {}, //当前用户
      rolesList: [], //option的数据
      isbji: false, //控制编辑的模态框
      formLabelWidth: "80px",
      queryinfo: {
        query: "",
        pagenum: 1,
        pagesize: 3
      },
      from: {
        username: "",
        password: "",
        email: "",
        mobile: ""
      },
      rules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" },
          { min: 3, max: 10, message: "长度在  3到 10个字符", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { min: 3, max: 18, message: "长度在  3到 18个字符", trigger: "blur" }
        ],
        email: [{ validator: email, trigger: "blur" }],
        mobile: [{ validator: mobile, trigger: "blur" }]
      }
    };
  },
  created() {
    this.getlist();
  },

  methods: {
      //点击确定按钮分配角色给用户
   async fpeijse() {
        let {id}=this.xinjse
        let cid=this.statusfrom.cid
        // console.log(id,cid);
        //调用分配角色后台接口
        let res = await getFpei(id,cid)
        
        //隐藏对话框
        this.showjse=false
        //更新用户列表
        this.getlist()
    },

    //打开用户角色对话框
    async changejse(row) {
      console.log(row);
      this.xinjse = row;
      //调用户角色列表接口，渲染下拉菜单
      let res = await getjse();
      console.log(res);
      this.rolesList = res;

      this.showjse = true;
    },
    //监听用户状态改变
    async changeStatus(row) {
      //   console.log(row);
      let { id, mg_state } = row;
      let res = await changeStatus(id, mg_state);
    },
    //获取列表数据
    async getlist() {
      let res = await getuserApi(this.queryinfo);
      console.log(res);
      let { total, users } = res;
      this.list = users;
      this.total = total;
    },
    //条数
    handleSizeChange(val) {
      this.queryinfo.pagesize = val;
      this.getlist();
    },
    //页数
    handleCurrentChange(val) {
      this.queryinfo.pagenum = val;
      this.getlist();
    },
    //搜索,防抖,通过lodash插件 _.debounce防抖，_.throttle节流
    seach: _.debounce(function() {
      this.getlist();
    }, 300),
    //点击添加用户，打开模态框
    add() {
    //   先清空表单数据
      this.from={}
      this.isshow = true;
    },
    // 点击确定按钮添加数据
    addok() {
      this.$refs.ruleForm.validate(async valid => {
        if (valid) {
          //为true调用接口添加
          let res = await addApi(this.from);
          this.isshow = false;
          //渲染数据
          this.getlist();
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    //打开编辑对话框,回填数据
    edDakai(row) {
      this.isbji = true;
      console.log(row);
      this.from = row;
    },
    //点击确定按钮修改数据
    edit() {
      this.$refs.ybanji.validate(async valid => {
        if (valid) {
          //调用后台更新接口
          console.log(this.from);
          let { id, email, mobile } = this.from;
          let res = await editApi(id, { email, mobile });

          this.isbji = false;

          this.getlist();
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },

    //删除
    async del(row) {
      let id = row.id;
      let res = await getdelApi(id);
      this.getlist();
    }
  }
};
</script>

<style scoped >
</style>
