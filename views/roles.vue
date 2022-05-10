<template>
    <div>
        <!-- 面包屑区 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>权限管理</el-breadcrumb-item>
            <el-breadcrumb-item>角色列表</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- 卡片 -->
        <el-card class="m10">
            <!-- //添加角色 -->
            <el-button
                type="primary"
                @click="addkai"
            >添加角色</el-button>

            <!-- //表格 -->
            <el-table
                :data="list"
                border
                style="width: 100%"
                class="m10"
            >
                <el-table-column
                    prop="date"
                    type="expand"
                    label=""
                    width="60"
                >
                    <template slot-scope="scope">
                        <div v-if="scope.row.children.length>0">
                            <el-row
                                v-for="item1 in scope.row.children"
                                :key="item1.id"
                                class="row_box"
                            >
                                <el-col :span="6">
                                    <el-tag
                                        type="primary"
                                        closable
                                        @close="deleterow(scope.row.id,item1.id)"
                                    >{{item1.authName}}</el-tag>
                                </el-col>
                                <el-col :span="18">
                                    <el-row
                                        v-for="item2 in item1.children"
                                        :key="item2.id"
                                    >
                                        <el-col :span="6">
                                            <el-tag
                                                closable
                                                @close="deleterow(scope.row.id,item2.id)"
                                                type="warning"
                                            >{{item2.authName}}</el-tag>
                                        </el-col>
                                        <el-col :span="18">
                                            <el-tag
                                                type="success"
                                                closable
                                                @close="deleterow(scope.row.id,item3.id)"
                                                v-for="item3 in item2.children"
                                                :key="item3.id"
                                            >{{item3.authName}}</el-tag>
                                        </el-col>
                                    </el-row>
                                </el-col>
                            </el-row>
                        </div>
                        <div v-else>dsfgf</div>
                    </template>
                </el-table-column>
                <el-table-column
                    type="index"
                    prop="name"
                    label="#"
                    width="60"
                >
                </el-table-column>
                <el-table-column
                    prop="roleName"
                    label="角色名称"
                >
                </el-table-column>
                <el-table-column
                    prop="roleDesc"
                    label="角色描述"
                >
                </el-table-column>
                <el-table-column
                    prop="address"
                    label="操作"
                >
                    <template slot-scope="scope">
                        <div>
                            <!-- //编辑的按钮 -->
                            <el-button
                                type="primary"
                                size="mini"
                                @click="edit(scope.row)"
                            >编辑</el-button>
                            <!-- //删除的按钮 -->
                            <el-button
                                type="danger"
                                size="mini"
                                @click="del(scope.row.id)"
                            >删除</el-button>
                            <!-- 用户分配权限的按钮 -->
                            <el-button
                                type="warning"
                                size="mini"
                                @click="kfpei(scope.row)"
                            >分配权限</el-button>

                        </div>
                    </template>

                </el-table-column>
            </el-table>

        </el-card>

        <!-- //添加的对话框 -->
        <el-dialog
            title="添加角色"
            :visible.sync="isshow"
        >
            <el-form :model="form">
                <el-form-item
                    label="角色名称"
                    :label-width="formLabelWidth"
                >
                    <el-input
                        autocomplete="off"
                        v-model="form.roleName"
                    ></el-input>
                </el-form-item>
                <el-form-item
                    label="角色描述"
                    :label-width="formLabelWidth"
                >
                    <el-input
                        v-model="form.roleDesc"
                        autocomplete="off"
                    ></el-input>
                </el-form-item>

            </el-form>
            <div
                slot="footer"
                class="dialog-footer"
            >
                <el-button @click="isshow = false">取 消</el-button>
                <el-button
                    type="primary"
                    @click="add"
                >确 定</el-button>
            </div>
        </el-dialog>

        <!-- //编辑的对话框 -->
        <el-dialog
            title="修改角色"
            :visible.sync="isedit"
        >
            <el-form :model="form">
                <el-form-item
                    label="角色名称"
                    :label-width="formLabelWidth"
                >
                    <el-input
                        autocomplete="off"
                        v-model="form.roleName"
                    ></el-input>
                </el-form-item>
                <el-form-item
                    label="角色描述"
                    :label-width="formLabelWidth"
                >
                    <el-input
                        v-model="form.roleDesc"
                        autocomplete="off"
                    ></el-input>
                </el-form-item>

            </el-form>
            <div
                slot="footer"
                class="dialog-footer"
            >
                <el-button @click="isedit = false">取 消</el-button>
                <el-button
                    type="primary"
                    @click="addEdit"
                >确 定</el-button>
            </div>
        </el-dialog>

        <!-- 分配权限对话框 -->
        <el-dialog
            title="提示"
            :visible.sync="dialogVisible"
            width="30%"
            :before-close="handleClose"
        >
            <!-- //data数据源 -->
            <!--  show-checkbox是否显示复选框 -->
            <!--    node-key="id"指定唯一标识 -->
            <!--  :default-expanded-keys默认展开的id -->
            <!--  :default-checked-keys="[5]" 默认选中的-->
            <!--  :props="defaultProps"设置默认配置 label：节点名称 children：子级属性名称-->
            <el-tree
                ref="treeRef"
                :data="tree"
                show-checkbox
                node-key="id"
                default-expand-all
                :default-expanded-keys="[2, 3]"
                :default-checked-keys="xzhong"
                :props="defaultProps"
            >
            </el-tree>

            <span
                slot="footer"
                class="dialog-footer"
            >
                <el-button @click="dialogVisible = false">取 消</el-button>
                <el-button
                    type="primary"
                    @click="huoqu"
                >确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import {
  getjseListApi,
  addJseApi,
  addEdijseApi,
  deleteJseApi,
  qxianlistApi,
  jseSquanApi,
  deleteQxianApi
} from "../http/api.js";
export default {
  data() {
    return {
      list: [],
      isshow: false,
      isedit: false,
      jseid: 0, //当前角色id
      formLabelWidth: "100px",
      xzhong: [], //默认选中id的数组
      dialogVisible: false, //控制分配权限模态框
      defaultProps: {
        label: "authName",
        children: "children"
      }, //tree默认配置
      tree: [], //属性数据
      form: {
        roleName: "",
        roleDesc: ""
      }
    };
  },
  created() {
    this.getlist();
  },
  methods: {
    async getlist() {
      let res = await getjseListApi();
      console.log(res);
      this.list = res;
    },

    // 打开添加角色模态框
    addkai() {
      this.form = {};
      this.isshow = true;
    },
    //添加角色
    async add() {
      let res = await addJseApi(this.form);
      this.getlist();
      this.isshow = false;
    },

    // 编辑回填
    async edit(row) {
      console.log(row);
      this.isedit = true;
      this.form = row;
    },
    // 添加编辑角色
    async addEdit() {
      console.log(this.form);
      let { id, roleName, roleDesc } = this.form;
      let res = await addEdijseApi(id, { roleName, roleDesc });
      this.isedit = false;
      this.getlist;
    },
    //删除功能
    async del(id) {
      let res = await deleteJseApi(id);
      this.getlist();
    },

    //打开分配权限对话框，掉权限列表接口
    async kfpei(row) {
      let res = await qxianlistApi("tree");
      console.log(res);
      this.tree = res;
      this.jseid = row.id;
      //递归
      this.getshuarr(row, this.xzhong);
      this.dialogVisible = true;
    },
    getshuarr(row, xzhong) {
      if (!row.children) {
        return xzhong.push(row.id);
      }
      row.children.forEach(item => {
        this.getshuarr(item, xzhong);
      });
    },
    //点击确定按钮拿到全选和半选
    async huoqu() {
      // 全选的id,getCheckedKeys element的方法
      let qxuan = this.$refs.treeRef.getCheckedKeys();
      //半选的id element的方法
      let bxuan = this.$refs.treeRef.getHalfCheckedKeys();
      //将全选半选的id合并
      let merge = [...qxuan, ...bxuan];
      // 将合并成的数组转为字符串
      let shuarr = merge.join(",");
      // 掉角色授权接口
      let res = await jseSquanApi(this.jseid, shuarr);
      console.log(res);
      this.getlist();
      this.dialogVisible = false;
    },

    //删除权限
    async deleterow(roleId, rightId) {
      let res = await deleteQxianApi(roleId, rightId);
      //   row.children=res
      this.getlist();
    },

    // 监听分配关闭对话框
    handleClose() {
      this.xzhong = [];
    }
  }
};
</script>

<style scoped >
/* .row_box{
    margin: 10px;
} */
</style>
