<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>分类参数</el-breadcrumb-item>
    </el-breadcrumb>
    <!--  -->
    <el-card class="m10">
      <el-alert
        title="警告提示的文案"
        type="warning"
        show-icon
        class="m10"
      >
      </el-alert>

      <!--  -->
      <el-row class="m10">
        <el-col
          :span="3"
          class="tt"
        >
          <label for="">选择商品分类 :</label>
        </el-col>
        <el-col :span="21">
          <el-cascader
            v-model="idcanshu"
            :options="canList"
            :props="defaultProps"
            @change="handleChange"
          ></el-cascader>
        </el-col>
      </el-row>

      <!-- 动态参数 -->
      <el-tabs
        v-model="activeName"
        @tab-click="handleClick"
      >
        <el-tab-pane
          label="用户管理"
          name="many"
        >
          <el-button
            type="primary"
            :disabled="isOK"
            @click="dkai"
          >添加参数</el-button>

          <!--  -->
          <el-table
            :data="many"
            border
            style="width: 100%"
            class="m10"
          >
            <el-table-column
              prop="date"
              width="60"
              type="expand"
            >
              <template slot-scope="scope">
                <div>
                  <el-tag
                    class="tag"
                    v-for="(item,index) in scope.row.attr_vals"
                    :key="index"
                    closable
                    :disable-transitions="false"
                    @close="handleClose(index,scope.row)"
                  >
                    {{item}}
                  </el-tag>
                  <el-input
                    class="input-new-tag"
                    v-if="inputVisible"
                    v-model="inputValue"
                    ref="saveTagInput"
                    size="small"
                    @keyup.enter.native="handleInputConfirm(scope.row)"
                    @blur="handleInputConfirm(scope.row)"
                  >
                  </el-input>
                  <el-button
                    v-else
                    class="button-new-tag"
                    size="small"
                    @click="showInput"
                  >+ New Tag</el-button>
                </div>
              </template>
            </el-table-column>
            <el-table-column
              prop="name"
              label="#"
              type="index"
              width="60"
            >
            </el-table-column>
            <el-table-column
              prop="attr_name"
              label="参数名称"
            >
            </el-table-column>
            <el-table-column
              prop="address"
              label="操作"
            >
              <template slot-scope="scope">
                <div>
                  <el-button type="primary" size="mini" @click="edit(scope.row)">编辑</el-button>
                  <el-button type="danger" size="mini" @click="dels(scope.row)">删除</el-button>
                </div>
              </template>
            </el-table-column>
          </el-table>

        </el-tab-pane>
        <!-- 静态参数 -->
        <el-tab-pane
          label="配置管理"
          name="only"
        >

          <el-button
            type="primary"
            :disabled="isOK"
            @click="jtaiok"
          >添加属性</el-button>

          <!--  -->
          <el-table
            :data="only"
            border
            style="width: 100%"
            class="m10"
          >
            <el-table-column
              prop="date"
              width="60"
              type="expand"
            >
              <template slot-scope="scope">
                <div>
                  <el-tag
                    class="tag"
                    v-for="(item,index) in scope.row.attr_vals"
                    :key="index"
                    closable
                    :disable-transitions="false"
                    @close="handleClose(index,scope.row)"
                  >
                    {{item}}
                  </el-tag>
                  <el-input
                    class="input-new-tag"
                    v-if="inputVisible"
                    v-model="inputValue"
                    ref="saveTagInput"
                    size="small"
                    @keyup.enter.native="handleInputConfirm(scope.row)"
                    @blur="handleInputConfirm(scope.row)"
                  >
                  </el-input>
                  <el-button
                    v-else
                    class="button-new-tag"
                    size="small"
                    @click="showInput"
                  >+ New Tag</el-button>
                </div>
              </template>
            </el-table-column>
            <el-table-column
              prop="name"
              label="#"
              type="index"
              width="60"
            >
            </el-table-column>
            <el-table-column
              prop="attr_name"
              label="参数名称"
            >
            </el-table-column>
            <el-table-column
              prop="address"
              label="操作"
            >
              <template slot-scope="scope">
                <div>
                  <el-button type="primary" @click="edit(scope.row)">编辑</el-button>
                  <el-button type="danger" @click="dels(scope.row)">删除</el-button>
                </div>
              </template>
            </el-table-column>
          </el-table>

        </el-tab-pane>

      </el-tabs>

    </el-card>

    <!-- 动态添加的模态框 -->
    <el-dialog title="添加动态参数" :visible.sync="addOK">
  <el-form :model="from">
    <el-form-item label="动态参数" :label-width="formLabelWidth">
      <el-input v-model="from.attr_name" autocomplete="off"></el-input>
    </el-form-item>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="addOK = false">取 消</el-button>
    <el-button type="primary" @click="addsOK">确 定</el-button>
  </div>
</el-dialog>


  <!-- 静态添加的模态框 -->
    <el-dialog title="添加动态参数" :visible.sync="jaddOK">
  <el-form :model="from">
    <el-form-item label="静态参数" :label-width="formLabelWidth">
      <el-input v-model="from.attr_name" autocomplete="off"></el-input>
    </el-form-item>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="jaddOK = false">取 消</el-button>
    <el-button type="primary" @click="jaddsOK">确 定</el-button>
  </div>
</el-dialog>

 <!-- 修改的模态框 -->
    <el-dialog title="修改参数" :visible.sync="isedit">
  <el-form :model="from">
    <el-form-item label="参数属性" :label-width="formLabelWidth">
      <el-input v-model="from.attr_name" autocomplete="off"></el-input>
    </el-form-item>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="isedit = false">取 消</el-button>
    <el-button type="primary" @click="editOK">确 定</el-button>
  </div>
</el-dialog>

  </div>
</template>

<script>
import { getGoodsCateList, getGoodsCshuApi, editCshuApi ,addCshuApi,deleteCshuApi} from "../http/api.js";
export default {
  data() {
    return {
      idcanshu: [],
      canList: [],
      defaultProps: {
        expandTrigger: "hover",
        children: "children",
        label: "cat_name",
        value: "cat_id"
      },
      activeName: "many", //联动tab页
      many: [],
      only: [],
      inputVisible: false,
      inputValue: "",
      addOK:false,
      from:{
        attr_name:"",
        attr_id:"",
        attr_vals:""
      },
      formLabelWidth:"100px",
      jaddOK:false,
      isedit:false,
    };
  },
  created() {
    this.getlist();
  },
  computed: {
    isOK() {
      return this.idcanshu.length !== 3;
    },
    //当前分类id，级联的最后一位
    cateId() {
      return this.idcanshu[this.idcanshu.length - 1];
    }
  },
  methods: {
    async getlist() {
      let res = await getGoodsCateList();
      console.log(res);
      this.canList = res;
    },
    //三级分类触发的方法
    handleChange() {
      this.getGoodCate();
    },
    //点击tab页触发的方法
    handleClick() {
      this.getGoodCate();
    },
    async getGoodCate() {
      console.log("dfxcc");
      if (this.idcanshu.length !== 3) {
        this.$message.error("请选择三级分类列表");
        return false;
      }
      //掉商品参数接口
      let res = await getGoodsCshuApi(this.cateId, this.activeName);
      console.log(res);

      if (this.activeName === "many") {
        res.forEach(item => {
          item.attr_vals =
            item.attr_vals.length > 0 ? item.attr_vals.split(",") : [];
          // item.inputVisible = false;
          // item.inputValue = "";
        });
        this.many = res;
      } else if (this.activeName === "only") {
        res.forEach(item => {
          item.attr_vals =
            item.attr_vals.length > 0 ? item.attr_vals.split(",") : [];
          // item.inputVisible = false;
          // item.inputValue = "";
        });
        this.only = res;
      }
    },
    //按下回车添加触发
    async handleInputConfirm(row) {
      console.log(row);
      if (this.inputValue.trim().length > 0) {
        row.attr_vals.push(this.inputValue);
        this.inputVisible = false;
        this.inputValue = "";
        let res = await editCshuApi(this.cateId, row.attr_id, {
          attr_name: row.attr_name,
          attr_sel: this.activeName,
          attr_vals: row.attr_vals.join(",")
        });
      }
    },
    //点击叉号删除
   async handleClose(index,row) {
      row.attr_vals.splice(index,1)
       let res = await editCshuApi(this.cateId, row.attr_id, {
          attr_name: row.attr_name,
          attr_sel: this.activeName,
          attr_vals: row.attr_vals.join(",")
        });
    },
    //点击New按钮
    showInput() {
      this.inputVisible = true;
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus();
      });
    },
    //动态
    dkai(){
      this.from={}
      this.addOK=true
    },
    //静态
    jtaiok(){
      this.from={}
      this.jaddOK=true
    },
    //动态点击确定按钮添加参数
   async addsOK(){
      if(this.from.attr_name==""){
          this.$message.error('参数名不能为空');
          return false
      }
      let res=await addCshuApi(this.cateId,{
        attr_name:this.from.attr_name,
        attr_sel:this.activeName
      })
      this.getGoodCate()
      this.addOK=false
    },
      //静态点击确定按钮添加参数
   async jaddsOK(){
      if(this.from.attr_name==""){
          this.$message.error('参数名不能为空');
          return false
      }
      let res=await addCshuApi(this.cateId,{
        attr_name:this.from.attr_name,
        attr_sel:this.activeName
      })
      this.getGoodCate()
      this.jaddOK=false
    },
    //编辑回填
    edit(row){
      console.log(row);
      this.from.attr_name=row.attr_name
      this.from.attr_id=row.attr_id
      this.from.attr_vals=row.attr_vals
      this.isedit=true
    },

    async editOK(){
      let res = await editCshuApi(this.cateId,this.from.attr_id,{
        attr_name:this.from.attr_name,
        attr_sel:this.activeName,
        
      })
      this.getGoodCate()
      this.isedit=false
    },
  
   async dels(row){
      console.log(row);
      let res=await deleteCshuApi(this.cateId,row.attr_id)
      this.getGoodCate()
    }
  }
};
</script>
<style>
.el-cascader-menu__list {
  height: 300px;
}
</style>

<style scoped >
.tt {
  margin-top: 10px;
}
.input-new-tag {
  width: 100px;
}
.tag {
  margin: 6px;
}
</style>
