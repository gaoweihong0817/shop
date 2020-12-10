<template>
  <div class="gwh_index">
    <!-- 布局 -->
    <el-container>
      <el-header>
        <h1>admin后台管理系统</h1>
        <el-button type="info" @click="edit">退出</el-button>
      </el-header>
      <el-container>
        <el-aside :width="isCollapse ? '64px':'200px'">
          <!-- 点击，收起左侧菜单栏 -->
          <div class="gwh_box" @click="Collapse">|||</div>
          <!-- 左侧菜单栏 -->
          <el-col>
            <el-menu
              default-active="2"
              class="el-menu-vertical-demo"
              background-color="#545c64"
              text-color="#fff"
              active-text-color="#ffd04b"
              router
              :collapse="isCollapse"
              :collapse-transition="false"
              unique-opened
            >
              <el-submenu :index="item.path" v-for="(item,index) in numeList" :key="index">
                <template slot="title">
                  <i :class="iconId[item.id]"></i>
                  <span>{{item.authName}}</span>
                </template>
                <el-menu-item-group>
                  <el-menu-item :index="it.path" v-for="(it,inx) in item.children" :key="inx">
                    <template>
                      <i class="el-icon-menu"></i>
                      <span>{{it.authName}}</span>
                    </template>
                  </el-menu-item>
                </el-menu-item-group>
              </el-submenu>
            </el-menu>
          </el-col>
        </el-aside>
        <el-main>
          <!-- 占位符 -->
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 渲染左侧
      numeList: [],
      isCollapse: false,
      //渲染icon 根据id
      iconId: {
        "125": "el-icon-s-custom",
        "103": "el-icon-present",
        "101": "el-icon-s-goods",
        "102": "el-icon-tickets",
        "145": "el-icon-chat-dot-square"
      }
    };
  },
  methods: {
    //渲染左侧 菜单栏
    getnumeList() {
      this.$http.get("menus").then(res => {
        // console.log(res);
        this.numeList = res.data;
      });
    },
    //点击，收起左侧菜单栏
    Collapse() {
      this.isCollapse = !this.isCollapse;
    },
    //退出功能
    edit() {
      this.$confirm("要退出页面", "是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          localStorage.removeItem("nume");
          this.$router.push("/login");
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消退出"
          });
        });
    }
  },
  mounted() {
    this.getnumeList();
  }
};
</script>

<style scoped lang='scss'>
.gwh_index {
  width: 100%;
  height: 100%;
}
.el-container {
  width: 100%;
  height: 100%;
}
.el-header {
  background-color: #373d41;
  color: #333;
  line-height: 60px;
  position: relative;
  color: #fff;
  .el-button {
    position: absolute;
    right: 20px;
    bottom: 10px;
  }
}

.el-aside {
  background-color: #545c64;
  color: #333;
  text-align: center;
  line-height: 60px;
}

.el-main {
  background-color: #e9eef3;
  color: #333;
}
.gwh_box {
  height: 30px;
  background: #333;
  line-height: 30px;
  color: #fff;
}
.el-menu {
  border: none;
}
</style>