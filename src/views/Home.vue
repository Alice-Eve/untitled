<template>
  <div>
    <el-container>
      <el-header>
        <div>
            <a href="#"><img src="../assets/logo.png"></a>
        </div>
        <div class="sidebar">
            <!-- 点击展开收起导航和切换对应图标 -->
            <i class="el-icon-s-fold collapse-icon" @click="handle" v-show="block"></i>
            <i class="el-icon-s-unfold collapse-icon" @click="handle" v-show="toggle"></i>
        </div>
        <div>
          <el-dropdown class="userInfo" @command="commandHandler">
            <span class="el-dropdown-link">
              <i><img :src="user.userface" alt=""></i>{{user.name}}
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item command="userinfo">个人中心</el-dropdown-item>
              <el-dropdown-item command="setting">设置</el-dropdown-item>
              <el-dropdown-item command="logout" divided>注销登录</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </div>
      </el-header>
      <el-container>
        <el-aside width="collapse">
          <el-menu router unique-opened class="el-menu-vertical" :collapse="isCollapse">
            <el-submenu :index="index+''" v-for="(item,index) in routes" v-if="!item.hidden" :key="index">
              <template slot="title">
                <i style="color: #409eff;margin-right: 8px" :class="item.iconCls"></i>
                <span>{{item.name}}</span>
              </template>
              <el-menu-item :index="child.path" v-for="(child,indexj) in item.children" :key="indexj">
                {{child.name}}
              </el-menu-item>
            </el-submenu>
          </el-menu>
        </el-aside>
        <el-main>
          <el-breadcrumb separator-class="el-icon-arrow-right" v-if="this.$router.currentRoute.path!='/home'">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>{{this.$router.currentRoute.name}}</el-breadcrumb-item>
          </el-breadcrumb>
          <div class="homeWelcome" v-if="this.$router.currentRoute.path=='/home'">
            欢迎来到微人事！
          </div>
          <router-view class="homeRouterView"/>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>

export default {
  name: 'Home',
  data(){
    return {
      // users: JSON.parse(window.sessionStorage.getItem("user")),
      isCollapse: false, //导航栏默认为展开
      toggle: false,//第二个图标默认隐藏
      block: true,//默认显示第一个图标
    }
  },
  computed: {
    routes() {
      return this.$store.state.routes;
    },
    user() {
      return this.$store.state.currentHr;
    }
  },
  components: {
  },
  methods: {
    commandHandler(cmd) {
      if (cmd == 'logout') {
        this.$confirm('此操作将注销登录, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.getRequest("/logout");
          window.sessionStorage.removeItem("user")
          this.$store.commit('initRoutes', []);
          this.$router.replace("/");
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消操作'
          });
        });
      }else if (cmd == 'userinfo') {
        this.$router.push('/hrinfo');
      }
    },
    handle() {
      this.isCollapse = !this.isCollapse;
      this.toggle = !this.toggle;
      this.block = !this.block;
    }
  }
}
</script>

<style>

.el-menu-vertical:not(.el-menu--collapse) {
  width: 250px;
}
.el-header{
  background-color: #409eff;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.homeRouterView {
  margin-top: 10px;
}
.el-header img {
  height: 40px;
  width: 177px;
}
.el-dropdown-link img {
  width: 48px;
  height: 48px;
  border-radius: 24px;
  margin-right: 8px;
}
.el-dropdown-link {
  display: flex;
  align-items: center;
}
.sidebar {
  padding: 0;
  position: absolute;
  left: 210px;
}
.collapse-icon {
  cursor: pointer;
  font-size: 20px;
  color: #fff;
  padding: 12px 6px;
}
.userInfo {
  cursor: pointer;
}
</style>
