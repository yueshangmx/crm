<template>
  <div class="dept">
    <div class="g-top">
      <mt-header title="部门设置">
        <router-link to="/member" slot="left">
          <mt-button icon="back"></mt-button>
        </router-link>
      </mt-header>
    </div>
    <div class="g-main">
      <mt-loadmore :top-method="loadTop" ref="loadmore">
        <ul class="g-list">
          <li v-for="(item,index) in deptlist" :key="index" class="item-list">
            <div class="g-lefr">{{item.dept_name}}</div>
            <div class="g-right">
              <i class="iconfont icon-yijianfankui" @click="editDept(item.dept_name,item.dept_id)"></i>
              <i class="iconfont icon-delete" @click="deleteDept(item.dept_id)"></i>
            </div>
          </li>
        </ul>
      </mt-loadmore>
    </div>
    <div class="g-add" @click="addDept">
      <i class="iconfont icon-jiajianzujianjiahao"></i>
    </div>
  </div>
</template>

<script>
export default {
  name: "dept",
  data() {
    return {
      deptlist: []
    };
  },
  methods: {
    getDeptlist() {
      this.$http
        .post(
          this.$store.state.SERVER + "/data/dept.php",
          this.$Qs.stringify({
            user_id: this.$store.state.userinfo.user_parent_id
          })
        )
        .then(
          function(res) {
            if (res.data.length > 0) {
              this.deptlist = res.data;
            } else {
              this.$toast({
                message: "读取出错！",
                iconClass: "iconfont icon-cuowu"
              });
            }
          }.bind(this)
        );
    },
    loadTop() {
      this.getDeptlist();
      this.$refs.loadmore.onTopLoaded();
    },
    editDept(name, id) {
      this.$messagebox.prompt("确定将" + name + "更名吗？", "").then(data => {
        this.$http
          .post(
            this.$store.state.SERVER + "/data/dept.php",
            this.$Qs.stringify({
              user_id: this.$store.state.userinfo.user_id,
              dept_id: id,
              new_dept_name: data.value
            })
          )
          .then(
            function(res) {
              if (res.data.result) {
                this.$toast({
                  message: "修改成功！",
                  iconClass: "iconfont icon-icon31"
                });
                this.getDeptlist();
              } else {
                this.$toast({
                  message: "修改失败",
                  iconClass: "iconfont icon-cuowu"
                });
              }
            }.bind(this)
          );
      });
    },
    deleteDept(data) {
      this.$messagebox.confirm("确定删除此部门吗？").then(() => {
        this.$http
          .post(
            this.$store.state.SERVER + "/data/dept.php",
            this.$Qs.stringify({
              user_id: this.$store.state.userinfo.user_id,
              dept_id: data
            })
          )
          .then(
            function(res) {
              if (res.data.result) {
                this.$toast({
                  message: "删除成功！",
                  iconClass: "iconfont icon-icon31"
                });
                this.getDeptlist();
              } else {
                this.$toast({
                  message: "删除失败",
                  iconClass: "iconfont icon-cuowu"
                });
              }
            }.bind(this)
          );
      });
    },
    addDept() {
      this.$messagebox.prompt("请输入部门名称", "").then(data => {
        let new_name = data.value;
        this.$http
          .post(
            this.$store.state.SERVER + "/data/dept.php",
            this.$Qs.stringify({
              user_id: this.$store.state.userinfo.user_id,
              new_dept_name: new_name
            })
          )
          .then(
            function(res) {
              if (res.data.result) {
                this.$toast({
                  message: "添加成功！",
                  iconClass: "iconfont icon-icon31"
                });
                this.getDeptlist();
              } else {
                this.$toast({
                  message: "添加失败",
                  iconClass: "iconfont icon-cuowu"
                });
              }
            }.bind(this)
          );
      });
    }
  },
  created() {
    if (!this.$store.state.userinfo.user_id) {
      this.$store.commit("updateUserInfo");
    }
    this.getDeptlist();
  }
};
</script>

<style scoped lang="less">
.dept {
  .g-top {
    .mint-header {
      background-color: #df5420;
    }
  }
  .g-main {
    .g-list {
      margin-top: 0;
      list-style: none;
      padding-left: 0;
      padding-bottom: 100%;
      .item-list {
        font-size: 14px;
        border-bottom: 1px solid #ccc;
        padding: 12px 5px 12px 10px;
        background-color: #fff;
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-between;
        .g-right {
          i {
            margin-right: 15px;
            font-size: 18px;
            font-weight: bold;
          }
          .icon-delete {
            font-size: 20px;
          }
        }
      }
    }
  }
  .g-add {
    i {
      position: fixed;
      right: 30px;
      bottom: 50px;
      background: #df5430;
      width: 40px;
      height: 40px;
      border-radius: 50%;
      text-align: center;
      line-height: 40px;
      font-size: 1.5rem;
      color: #fff;
    }
  }
}
</style>
