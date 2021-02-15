<template>
  <div>
    <div style="margin-top: 10px;display: flex;justify-content: center">
      <el-input size="small" v-model="keywords" placeholder="通过用户名搜索" prefix-icon="el-icon-search" style="width: 400px;margin-right: 10px"></el-input>
      <el-button size="small" icon="el-icon-search" type="primary">搜索</el-button>
    </div>
    <div class="hr-container">
      <el-card class="hr-card" v-for="(hr,index) in hrs" :key="index">
        <div slot="header" class="clearfix">
          <span>{{ hr.name }}</span>
          <el-button style="float: right; padding: 3px 0;color: red" type="text" icon="el-icon-delete"></el-button>
        </div>
        <div>
          <div class="img-container">
            <img :src="hr.userface" :alt="hr.name" :title="hr.name" class="userface-img">
          </div>
          <div class="userinfo-container">
              <div>用户名:{{hr.name}}</div>
              <div>手机:{{hr.phone}}</div>
              <div>电话:{{hr.telephone}}</div>
              <div>地址:{{hr.address}}</div>
              <div>用户状态:<el-switch
                  v-model="hr.enabled"
                  @change="enabledChange(hr)"
                  active-text="启用"
                  active-color="#13ce66"
                  inactive-color="#ff4949"
                  inactive-text="禁用">
              </el-switch></div>
              <div>
                用户角色:
                <el-tag type="success" size="small" style="margin:3px 6px 0 0" v-for="(role,indexj) in hr.roles" :key="indexj">{{role.nameZh}}</el-tag>
                <el-popover
                    placement="bottom"
                    title="标题"
                    width="200"
                    trigger="click"
                    content="这是一段内容,这是一段内容,这是一段内容,这是一段内容。">
                  <el-button slot="reference">click 激活</el-button>
                </el-popover>
                <el-button slot="reference" icon="el-icon-more" type="text"></el-button>
              </div>
              <div>备注: {{hr.remark}}</div>
          </div>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script>
export default {
  name: "SysHr",
  data() {
    return {
      keywords:'',
      hrs:[],
      inputVisible: false,
      inputValue: ''
    }
  },
  methods:{
    enabledChange(hr) {
      delete hr.roles;
      this.putRequest("/system/hr/", hr).then(resp => {
        if (resp) {
          this.initHrs();
        }
      })
    },
    initHrs() {
      this.getRequest("/system/hr/").then(resp=>{
        if (resp) {
          this.hrs = resp;
        }
      })
    },
    showInput() {
      this.inputVisible = true;
      this.$nextTick(_ => {
        this.$refs.saveTagInput.$refs.input.focus();
      });
    },
    handleInputConfirm() {
      let inputValue = this.inputValue;
      if (inputValue) {
      }
      this.inputVisible = false;
      this.inputValue = '';
    }
  },
  mounted() {
    this.initHrs();
  }
}
</script>

<style>
  .userinfo-container div {
    font-size: 12px;
    color: #409eff;
  }
  .userinfo-container {
    margin-top: 20px;
  }
  .img-container {
    width: 100%;
    display: flex;
    justify-content: center;
  }
  .userface-img {
    width: 72px;
    height: 72px;
    border-radius: 72px;
  }
  .hr-container {
    margin-top: 10px;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
  .hr-card {
    width: 350px;
    margin-bottom: 20px;
  }
  .el-tag + .el-tag {
    margin-left: 10px;
  }
  .button-new-tag {
    margin-left: 10px;
    height: 32px;
    line-height: 30px;
    padding-top: 0;
    padding-bottom: 0;
  }
  .input-new-tag {
    width: 90px;
    margin-left: 10px;
    vertical-align: bottom;
  }
</style>