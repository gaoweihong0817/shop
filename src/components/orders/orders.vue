<template>
  <div class="gwh_orders">
    <!-- 面包屑 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/index' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>订单管理</el-breadcrumb-item>
      <el-breadcrumb-item>订单列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图 -->
    <el-card class="box-card">
      <!-- 搜索 -->
      <el-input placeholder="请输入内容">
        <el-button slot="append" icon="el-icon-search"></el-button>
      </el-input>
      <!-- 表格 -->
      <el-table :data="tableData" border style="width: 100%">
        <el-table-column label="#" type="index"></el-table-column>
        <el-table-column prop="order_number" label="订单编号"></el-table-column>
        <el-table-column prop="order_price" label="订单价格" width="80px"></el-table-column>

        <el-table-column prop="order_pay" label="是否付款" width="100px">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.order_pay==1" type="success">已付款</el-tag>
            <el-tag v-else type="danger">未付款</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="is_send" label="是否发货" width="80px"></el-table-column>
        <el-table-column prop="create_time" label="下单时间">
          <template slot-scope="scope">{{scope.row.create_time|dateFormat}}</template>
        </el-table-column>
        <el-table-column label="操作">
          <el-button type="primary" icon="el-icon-edit" size="mini" @click="edit"></el-button>
          <el-button type="success" icon="el-icon-location" size="mini" @click="search"></el-button>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="pagenum"
        :page-sizes="[5, 10, 15]"
        :page-size="pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      ></el-pagination>
      <!-- 修改地址 -->
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px">
        <el-dialog title="修改地址" :visible.sync="dialogVisible" width="50%">
          <el-form-item label="省市区/县" prop="name">
            <el-cascader v-model="value" :options="Citydata"></el-cascader>
          </el-form-item>
          <el-form-item label="详细地址" prop="name1">
            <el-input v-model="ruleForm.name1"></el-input>
          </el-form-item>
          <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
          </span>
        </el-dialog>
      </el-form>
      <!-- 物流信息 -->
      <el-dialog title="物流信息" :visible.sync="searchdialogVisible" width="50%">
        <!-- 时间线 -->
        <el-timeline>
          <el-timeline-item
            v-for="(activity, index) in options"
            :key="index"
            :timestamp="activity.times"
          >{{activity.context}}</el-timeline-item>
        </el-timeline>
        <span slot="footer" class="dialog-footer">
          <el-button @click="searchdialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="searchdialogVisible = false">确 定</el-button>
        </span>
      </el-dialog>
    </el-card>
  </div>
</template>

<script>
//修改地址
import Citydata from "./citydata";
export default {
  data() {
    return {
      //表格渲染
      tableData: [],
      //分页功能
      pagesize: 10, //每页的条数
      pagenum: 1, //当前页
      total: 0, //总页数
      //修改地址
      dialogVisible: false,
      ruleForm: {
        name1: "",
        name: ""
      },
      rules: {
        name1: [{ required: true, message: "请输入详细地址", trigger: "blur" }],
        name: [{ required: true, message: "请输入省市区/县", trigger: "blur" }]
      },
      Citydata,
      searchdialogVisible: false,
      //   reverse: true,
      options: [
        {
          time: "2018-05-10 09:39:00",
          ftime: "2018-05-10 09:39:00",
          context: "已签收,感谢使用顺丰,期待再次为您服务",
          location: ""
        },
        {
          time: "2018-05-10 08:23:00",
          ftime: "2018-05-10 08:23:00",
          context:
            "[北京市]北京海淀育新小区营业点派件员 顺丰速运 95338正在为您派件",
          location: ""
        },
        {
          time: "2018-05-10 07:32:00",
          ftime: "2018-05-10 07:32:00",
          context: "快件到达 [北京海淀育新小区营业点]",
          location: ""
        },
        {
          time: "2018-05-10 02:03:00",
          ftime: "2018-05-10 02:03:00",
          context:
            "快件在[北京顺义集散中心]已装车,准备发往 [北京海淀育新小区营业点]",
          location: ""
        },
        {
          time: "2018-05-09 23:05:00",
          ftime: "2018-05-09 23:05:00",
          context: "快件到达 [北京顺义集散中心]",
          location: ""
        },
        {
          time: "2018-05-09 21:21:00",
          ftime: "2018-05-09 21:21:00",
          context: "快件在[北京宝胜营业点]已装车,准备发往 [北京顺义集散中心]",
          location: ""
        },
        {
          time: "2018-05-09 13:07:00",
          ftime: "2018-05-09 13:07:00",
          context: "顺丰速运 已收取快件",
          location: ""
        },
        {
          time: "2018-05-09 12:25:03",
          ftime: "2018-05-09 12:25:03",
          context: "卖家发货",
          location: ""
        },
        {
          time: "2018-05-09 12:22:24",
          ftime: "2018-05-09 12:22:24",
          context: "您的订单将由HLA（北京海淀区清河中街店）门店安排发货。",
          location: ""
        },
        {
          time: "2018-05-08 21:36:04",
          ftime: "2018-05-08 21:36:04",
          context: "商品已经下单",
          location: ""
        }
      ]
    };
  },
  methods: {
    //获取订单列表的数据
    gettableData() {
      this.$http
        .get("orders?pagenum=" + this.pagenum + "&pagesize=" + this.pagesize)
        .then(res => {
          console.log(res);
          //渲染到页面
          this.tableData = res.data.goods;
          this.total = res.data.total;
        });
    },
    //分页功能
    handleSizeChange(size) {
      //显示每页条数
      this.pagesize = size;
      this.gettableData();
    },
    handleCurrentChange(num) {
      //显示当前页的数据
      this.pagenum = num;
      this.gettableData();
    },
    //修改地址
    edit() {
      this.dialogVisible = true;
    },
    //查看订单
    search() {
      this.searchdialogVisible = true;
      this.gettableData();
    }
  },
  mounted() {
    //调用
    this.gettableData();
  }
};
</script>

<style scoped>
.el-card,
.el-pagination,
.el-table {
  margin-top: 2%;
}
.el-input {
  width: 480px;
}
.el-cascader {
  width: 100%;
}
</style>