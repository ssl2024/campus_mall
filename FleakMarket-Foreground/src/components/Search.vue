<template>
  <div class="Search">
    <el-row>
      <el-col :span="5" :offset="4">
        <router-link to="/">
          <img class="logo" src="../../static/img/school_icon.png" alt="" />
          <span>重师校园二手交易市场</span>
        </router-link>
      </el-col>
      <el-col :span="8">
        <div class="inpbtn">
          <input type="text" v-model="serachText" />
          <button @click="SearchText">搜索</button>
        </div>
      </el-col>
      <el-col :span="6">
        <span class="userImg" @click="gotoMine">
          <img :src="imgPath" />
        </span>
        <span class="userName" v-if="isNotLogin" @click="gotoLogin('login')"
          >登录</span
        >
        <span class="userName" v-if="isNotLogin" @click="gotoLogin('register')"
          >注册</span
        >
        <span class="userName" v-if="!isNotLogin" @click="gotoMine">{{
          userInfo.username
        }}</span>
        <span class="userName" v-if="!isNotLogin" @click="gooutLogin()"
          >退出登录</span
        >
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  data() {
    return {
      id: 0,
      userInfo: "",
      serachText: "", //带参查询
      isNotLogin: true,
      imgPath: "../../static/img/akari.jpg"
    };
  },
  created() {
    this.id = this.$store.state.userid;
    //加载用户登录信息
    if (this.id != 0) {
      this.$axios
        .get("/user/selectUserById", {
          params: {
            id: this.$store.state.userid
          }
        })
        .then(res => {
          this.userInfo = res.data;
          this.isNotLogin = false;
          if (res.data.userimgpath != null) {
            this.imgPath = res.data.userimgpath;
          }
        });
    }
  },
  methods: {
    //跳转我的账户页面
    gotoMine() {
      //判断用户是否登录
      if (this.id == 0) {
        this.$message({
          message: "请登录",
          type: "warning"
        });
      } else {
        this.$router.push({ name: "mine" });
      }
    },
    //跳转登陆页面
    gotoLogin(data) {
      this.$router.push({
        name: "login",
        query: {
          name: data
        }
      });
    },
    //退出登录
    gooutLogin() {
      this.$store.commit("userInfo", 0);
      this.$router.push({
        name: "login",
        query: {
          name: "login"
        }
      });
    },
    SearchText() {
      this.$router.push({
        name: "productType",
        query: {
          id: 0,
          serachText: this.serachText
        }
      });
    }
  }
};
</script>

<style scoped="scoped">
.Search {
  height: 100px;
  background-color: #ffffff;
  box-shadow: 0 1px 2px #e0e0e0;
  margin-bottom: 20px;
}
.logo {
  height: 60px;
  margin-top: 25px;
}

.logo + span {
  position: absolute;
  top: 10px;
  height: 91px;
  font-size: 24px;
  line-height: 91px;
  transform: translateX(10px);
}

.inpbtn {
  height: 20px;
  margin-top: 30px;
  display: flex;
}
.inpbtn input {
  width: 55%;
  height: 40px;
  padding: 0 10px;
  border: 2px solid #fa0;
}
.inpbtn button {
  width: 15%;
  height: 44px;
  color: #ffffff;
  font-size: 16px;
  font-weight: 800;
  cursor: pointer;
  background-color: #ff9900;
}
.inpbtn button:hover {
  background-color: #ff8800;
}
.userImg {
  display: block;
  float: left;
  margin-right: 10px;
  margin-top: 22px;
  cursor: pointer;
}
.userImg img {
  height: 48px;
  width: 48px;
  border-radius: 50%;
  border: 2px solid #eeeeee;
}
.userName {
  line-height: 100px;
  margin-left: 10px;
  color: #444;
  cursor: pointer;
}
.shopCart {
  line-height: 100px;
  color: #444;
  margin-left: 20px;
  cursor: pointer;
}
</style>
