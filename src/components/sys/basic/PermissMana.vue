<template>
  <div>
    <div class="permissManaTool">
      <el-input size="small" placeholder="请输入角色英文名" v-model="role.name">
        <template slot="prepend">ROLE_</template>
      </el-input>
      <el-input size="small" placeholder="请输入角色中文名" v-model="role.nameZh"></el-input>
      <el-button type="primary" size="small" icon="el-icon-plus" @click="addRole">添加角色</el-button>
    </div>
    <div class="PermissManaMain">
      <el-collapse v-model="activeNames" accordion @change="change">
        <el-collapse-item :title="r.nameZh" :name="r.id" v-for="(r,index) in roles" :key="index">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>可访问资源</span>
              <el-button
                  style="float: right; padding: 3px 0;color: #ff0000;"
                  icon="el-icon-delete"
                  type="text"
                  @click="deleteRole(r)"></el-button>
            </div>
            <div>
              <el-tree
                  show-checkbox
                  node-key="id"
                  ref="tree"
                  :key="index"
                  :default-checked-keys="selectedMenus"
                  :data="allmenus"
                  :props="defaultProps">
              </el-tree>
              <div style="display: flex;justify-content: flex-end">
                <el-button size="mini" @click="cancelUpdate">取消修改</el-button>
                <el-button size="mini" type="primary" @click="doUpdate(r.id,index)">确认修改</el-button>
              </div>
            </div>
          </el-card>
        </el-collapse-item>
      </el-collapse>
    </div>
  </div>
</template>

<script>
export default {
  name: "PermissMana",
  data() {
    return{
      role:{
        name:'',
        nameZh:''
      },
      allmenus:[],
      selectedMenus:[],
      activeNames:-1,
      roles:[],
      defaultProps: {
        children: 'children',
        label: 'name'
      }
    }
  },
  mounted() {
    this.initRoles();
  },
  methods: {
    deleteRole(role) {
      this.$confirm('此操作将永久删除'+role.nameZh+'角色, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest("/system/basic/per/" + role.id).then(resp => {
          if (resp) {
            this.initRoles();
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    addRole() {
      if (this.role.name && this.role.nameZh){
        this.postRequest("/system/basic/per/role",this.role).then(resp=>{
          if (resp) {
            this.role.name = '';
            this.role.nameZh = '';
            this.initRoles();
          }
        })
      } else {
        this.$message.error("角色不可为空")
      }
    },
    cancelUpdate() {
      this.activeNames = -1;
    },
    doUpdate(rid,index) {
      let tree = this.$refs.tree[index];
      let sel = tree.getCheckedKeys(true);
      let url = '/system/basic/per/?rid='+ rid;
      sel.forEach(key=>{
        url += '&mids=' + key;
      })
      this.putRequest(url).then(resp=>{
        if (resp) {
          this.activeNames = -1;
        }
      })
    },
    change(rid) {
      if (rid) {
        this.initAllMenus();
        this.initSelectedMenus(rid);
      }
    },
    initSelectedMenus(rid) {
      this.getRequest("/system/basic/per/mids/" + rid).then(resp=>{
        if (resp) {
          this.selectedMenus = resp;
        }
      })
    },
    initAllMenus(){
      this.getRequest("/system/basic/per/menus").then(resp=>{
        if (resp) {
          this.allmenus = resp;
        }
      })
    },
    initRoles() {
      this.getRequest("/system/basic/per/").then(resp=>{
        if (resp){
          this.roles = resp;
        }
      })
    }
  }
}
</script>

<style>
.PermissManaMain {
  margin-top: 30px;
  width: 800px;
}
.permissManaTool {
  display: flex;
  justify-content: flex-start;
}
.permissManaTool .el-input {
  width: 300px;
  margin-right: 6px;
}

</style>