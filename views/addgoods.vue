<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>商品管理</el-breadcrumb-item>
            <el-breadcrumb-item>添加商品</el-breadcrumb-item>
        </el-breadcrumb>

        <el-card class="m10">
            <el-alert
                title="消息提示的文案"
                type="info"
                :closable="false"
                center
                show-icon
            >
            </el-alert>

            <!-- 步骤条 -->
            <el-steps
                :active="active*1"
                finish-status="success"
                class="m10"
                align-center
            >
                <el-step title="基本信息"></el-step>
                <el-step title="商品参数"></el-step>
                <el-step title="商品属性"></el-step>
                <el-step title="商品图片"></el-step>
                <el-step title="商品内容"></el-step>
                <el-step title="完成"></el-step>
            </el-steps>

            <!-- 表单 -->
            <el-form :model="from">
                <!-- tab页 -->
                <el-tabs
                    tab-position="left"
                    v-model="active"
                    @tab-click="handleClick"
                    class="m10"
                    :before-leave="beforeLeave"
                >
                    <el-tab-pane
                        label="基本信息"
                        name="0"
                    >
                        <el-form-item label="商品名称">
                            <el-input v-model="from.goods_name"></el-input>
                        </el-form-item>
                        <el-form-item label="商品价格">
                            <el-input v-model="from.goods_price"></el-input>
                        </el-form-item>
                        <el-form-item label="商品重量">
                            <el-input
                                v-model="from.goods_weight"
                                type="number"
                            ></el-input>
                        </el-form-item>
                        <el-form-item label="商品数量">
                            <el-input v-model="from.goods_number"></el-input>
                        </el-form-item>
                        <el-form-item label="商品分类">
                            <!-- //商品分类 -->
                            <!-- v-model 当前选中数组-->
                            <!-- :options 级联选择框数据源-->
                            <!--props下拉框的默认配置项  -->
                            <!-- change分类改变时触发 -->

                            <el-cascader
                                v-model="thisCheck"
                                :options="cate"
                                :props="defaultProps"
                                @change="handleChange"
                            ></el-cascader>
                        </el-form-item>
                    </el-tab-pane>
                    <!--  -->
                    <el-tab-pane
                        label="商品参数"
                        name="1"
                    >
                        <!-- //渲染商品动态参数 -->
                        <el-form-item
                            :label="item1.attr_name"
                            v-for="item1 in many"
                            :key="item1.id"
                        >
                            <el-checkbox-group v-model="item1.attr_vals">
                                <el-checkbox
                                    :label="item2"
                                    v-for="(item2,index) in item1.attr_vals"
                                    :key="index"
                                ></el-checkbox>
                            </el-checkbox-group>
                        </el-form-item>

                    </el-tab-pane>
                    <el-tab-pane
                        label="商品属性"
                        name="2"
                    >
                        <el-form-item
                            :label="item.attr_name"
                            v-for="item in only"
                            :key="item.id"
                        >
                            <el-input v-model="item.attr_name"></el-input>
                        </el-form-item>

                    </el-tab-pane>
                    <el-tab-pane
                        label="商品图片"
                        name="3"
                    >
                        <!-- //图片上传 -->
                        <el-upload
                            action="http://www.ysqorz.top:8888/api/private/v1"
                            :headers="headers"
                            :on-preview="handlePreview"
                            :on-remove="handleRemove"
                            :onsuccess="hangsuccess"
                           
                            list-type="picture"
                        >
                            <el-button
                                size="small"
                                type="primary"
                            >点击上传</el-button>
                            <div
                                slot="tip"
                                class="el-upload__tip"
                            >只能上传jpg/png文件，且不超过500kb</div>
                        </el-upload>

                    </el-tab-pane>
                    <el-tab-pane
                        label="商品内容"
                        name="4"
                    >
                        <quill-editor
                           
                            v-model="from.goods_introduce"
                        >
                        </quill-editor>
                        <el-button type="primary" class="m10" @click="add">添加商品</el-button>
                    </el-tab-pane>
                    <el-tab-pane
                        label="完成"
                        name="5"
                    ></el-tab-pane>
                </el-tabs>

            </el-form>

        </el-card>

    </div>
</template>

<script>
import { goodsCateApi, getGoodsCshuApi,getaddShop } from "../http/api.js";
export default {
  data() {
    return {
      active: "0",
      headers: {
        Authorization: sessionStorage.getItem("token")
      },

      //表单数据
      from: {
        goods_name: "",
        goods_price: "",
        goods_number: "",
        goods_weight: "",
        goods_cat: "",
        goods_introduce: "",
        pics: [],
        attrs: []
      },
      thisCheck: [], //当前选中的分类等级
      defaultProps: {
        expandTrigger: "hover",
        label: "cat_name",
        children: "children",
        value: "cat_id"
      }, //props下拉框的默认配置项
      cate: [], //级联分类数据
      many: [], //动态参数
      only: [] //静态参数
    };
  },
  created() {
    this.getcatelist();
  },
 
 //计算分类等级
  computed: {
    cateId() {
      return this.thisCheck[this.thisCheck.length - 1];
    }
  },
  methods: {
      //上传富文本添加渲染页面
     async add(){
          this.from.goods_cat=this.thisCheck.join(',')
          //动态参数获取
        //  let dtcshu=this.many.map(item=>{
        //      return {
        //       attr_id:item.attr_id,
        //       attr_value:item.attr_value.join(',')
        //     }
        //  })

         //静态参数处理
        //  let jtcshu=this.only.map(item=>{
        //     return {
        //       attr_id:item.attr_id,
        //       attr_value:item.attr_value.join(',')
        //     }
        //  })

         //合并静态动态参数
        //  let hbingcshu=[...dtcshu,...jtcshu]
        //   this.from.attrs=hbingcshu
        //   console.log(this.from.attrs);
          

          await getaddShop(this.from)
          this.$router.push('/goods')
          this.getcatelist();
      },
    //上传成功
    hangsuccess() {
      this.from.push({ pic: res.data.tmp_path });
    },
    //   删除上传
    handleRemove() {
      let index = this.from.pics.findIndex(item => {
        return item.pic === res.response.data.tmp_path;
      });
      this.from.pics.splice(index, 1);
    },
    //触发预览
    // handlePreview() {},
    //点击左侧tab页触发的方法
    async handleClick() {
      //打印查看下标
      console.log(this.active);
      if (this.active === "1") {
        //调取动态参数接口
        let res = await getGoodsCshuApi(this.cateId);
        this.many = res;
        res.forEach(item => {
          item.attr_vals =
            item.attr_vals.length > 0 ? item.attr_vals.split(",") : [];
        });
        console.log(res);
      } else if (this.active == 2) {
        //调取经态参数接口
        this.only = await getGoodsCshuApi(this.cateId, "only");
      }
    },
    //级联菜单的数据变化
    // handleChange() {},
    async getcatelist() {
      let res = await goodsCateApi();
      console.log(res);
      this.cate = res;
    },
    // tab切换之前是否阻止到下一步的处理
    beforeLeave(newValue, oldvalue) {
      if (oldvalue === "0" && this.thisCheck.length !== 3) {
        this.$message({
          type: "error",
          message: "请选择三级分类"
        });
        return false;
      }
    }
  }
};
</script>

<style>
.el-cascader-panel {
    height: 300px;
}
</style>


<style scoped lang="scss">
::v-deep .el-step__title {
  font-size: 12px;
}
::v-deep .ql-editor{
    height: 300px;
}


</style>
