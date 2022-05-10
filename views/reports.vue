<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>活动管理</el-breadcrumb-item>
            <el-breadcrumb-item>活动列表</el-breadcrumb-item>
        </el-breadcrumb>
        <el-card class="m10">
            <div
                class="mycharts"
                ref="mycharts"
            ></div>
        </el-card>

    </div>
</template>

<script>
import * as echarts from "echarts";
import { getTubiaoApi } from "../http/api.js";
export default {
  data() {
    return {
      mycharts: null,
      tubiao: {
        title: {
          text: "用户来源"
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross",
            label: {
              backgroundColor: "#E9EEF3"
            }
          }
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true
        },
        xAxis: [
          {
            boundaryGap: false
          }
        ],
        yAxis: [
          {
            type: "value"
          }
        ]
      }
    };
  },

  mounted() {
    this.getlist();
    window.addEventListener("resize", this.mycharts.resize);
  },
  methods: {
    async getlist() {
      let chartDom = this.$refs.mycharts;

      // 初始化
      this.mycharts = echarts.init(chartDom);

      //调接口
      let res = await getTubiaoApi();

      let option = { ...this.tubiao, ...res };

      //绘制图表
      this.mycharts.setOption(option);
    }
  }
};
</script>

<style scoped >
.mycharts {
  width: 100%;
  height: 500px;
}
</style>
