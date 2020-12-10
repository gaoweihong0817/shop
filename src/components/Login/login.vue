<template>
  <div class="login">
    <section>
      <el-form ref="form" :model="addData" label-width="80px" :rules="rules">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="addData.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="addData.password"></el-input>
        </el-form-item>
        <p>
          <el-button type="primary" @click="getLogin">登录</el-button>
          <el-button type="info">重置</el-button>
        </p>
      </el-form>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      addData: {
        username: "admin",
        password: "123456"
      },
      rules: {
        username: [
          { required: true, message: "请输入用户名", trigger: "blur" }
        ],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }]
      }
    };
  },
  methods: {
    getLogin() {
      if (this.username == "" || this.password == "") {
        alert("用户名和密码不能为空");
        return false;
      }
      var obj = {
        username: this.addData.username,
        password: this.addData.password
      };
      this.$http.post("login", obj).then(res => {
        if (res.meta.status == 200) {
          localStorage.setItem("nume", JSON.stringify(res.data));
          this.$message.success(res.meta.msg);
          this.$router.push("/index");
        } else {
          this.$message.error(res.meta.msg);
        }
      });
    }
  },
  mounted() {}
};
</script>

<style scoped lang='scss'>
.login {
  width: 100%;
  height: 100%;
  background: #2b4b6b;
  position: relative;
  section {
    width: 450px;
    height: 250px;
    background: #fff;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    padding: 30px;
    border-radius: 10px;
  }
  p {
    text-align: center;
  }
}
</style>