<template>
  <div class="home">
    <div class="regist">
      <label>
        用户名：
        <input
          ref="username"
          type="text"
          v-model="username"
          @keydown.enter="register"
          @input="btnState"
          autofocus
        />
      </label>
      <label>
        <span style="letter-spacing:16px;">密</span>码：
        <input type="password" v-model="userpass" @keydown.enter="register" @input="btnState" />
      </label>
      <button @click="register" :disabled="btnDisabled">注册</button>
    </div>
    <div class="refresh">
      <button @click="deleteAllUser">删除全部用户</button>
      <button @click="getList">刷新</button>
    </div>
    <div class="tableCon">
      <table border="1" align="center" cellpadding="10" cellspacing="0">
        <caption>用户信息</caption>
        <thead>
          <tr>
            <th>序号</th>
            <th>id</th>
            <th>用户名</th>
            <th>密码</th>
            <th>操作</th>
          </tr>
        </thead>
        <tbody>
          <tr v-if="!tableData.length">
            <td colspan="5" align="center">暂无数据</td>
          </tr>
          <tr
            v-for="(item, index) in tableData"
            :key="item.id"
            @click="edit({index:index,id:item.id,username:item.username,userpass:item.userpass})"
          >
            <td>{{index}}</td>
            <td>{{item.id}}</td>
            <td>{{item.username}}</td>
            <td>{{item.userpass}}</td>
            <td>
              <input type="button" value="修改" @click="mask=true" />
              <input type="button" value="删除" @click="deleteUser(item.id)" />
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="mask" v-show="mask">
      <div class="dialog">
        <div class="title">修改用户信息</div>
        <div class="con">
          <table border="1" align="center" cellpadding="0" cellspacing="0">
            <tr>
              <td>
                <input type="text" v-model="row.index" />
              </td>
              <td>
                <input type="text" v-model="row.id" />
              </td>
              <td>
                <input type="text" v-model="row.username" />
              </td>
              <td>
                <input type="text" v-model="row.userpass" />
              </td>
            </tr>
          </table>
        </div>
        <div class="oparate">
          <input type="button" value="取消" @click="cancel" />
          <input type="button" value="保存" @click="updateUser()" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "home",
  data() {
    return {
      tableData: [], //表格数据
      row: {}, //选中当前行数据
      username: "",
      userpass: "",
      mask: false, //遮罩层
      btnDisabled: true //按钮禁用
    };
  },
  mounted() {
    this.getList(); //获取用户列表
    
  },
  methods: {
    getList() {
      //获取用户列表
      this.axios.get("http://192.168.0.125:3000/getList").then(res => {
        if (res.data.code != 200) {
          return;
        }
        console.log(res.data.message);
        this.mask = false;
        this.tableData = res.data.data;
      });
    },
    btnState() {
      //按钮是否禁用
      if (this.username && this.userpass) {
        this.btnDisabled = false;
      } else {
        this.btnDisabled = true;
      }
    },
    register() {
      //注册
      var _this = this;
      // var userinfo = {
      //   username: this.username,
      //   userpass: this.userpass
      // };
      // this.axios.post("http://localhost:3000/register", this.qs.stringify(userinfo), {
      //   headers: {
      //     'Content-Type': 'application/x-www-form-urlencoded'
      //   }
      // }).then(res => {
      //   console.log(res)
      // })
      if (!this.username || !this.userpass) {
        alert("必须输入用户名和密码！");
        return;
      }
      this.axios
        .get(
          "http://192.168.0.125:3000/register?username=" +
            this.username +
            "&userpass=" +
            this.userpass
        )
        .then(res => {
          console.log(res.data.message);
          if (res.data.code != 200) {
            return;
          }
          _this.username = _this.userpass = ""; //清空输入框
          _this.$refs.username.focus(); //重新聚焦第一个输入框
          _this.btnState(); //按钮是否禁用
          _this.getList(); //获取用户列表
        });
    },
    deleteUser(id) {
      //删除用户
      //删除用户
      var confirmDelete = confirm("确认删除吗？");
      confirmDelete &&
        this.axios
          .get("http://192.168.0.125:3000/deleteUser?id=" + id)
          .then(res => {
            if (res.data.code != 200) {
              return;
            }
            console.log(res.data.message);
            this.getList(); //获取用户列表
          });
    },
    deleteAllUser() {
      //删除所有用户
      var confirmDelete = confirm("确认要删除所有用户吗？（谨慎操作！！！）");
      confirmDelete &&
        this.axios.get("http://192.168.0.125:3000/deleteAllUser").then(res => {
          console.log(res.data.message);
          this.getList(); //获取用户列表
        });
    },
    edit(data) {
      //编辑获取当前行数据
      this.row = data;
    },
    updateUser() {
      //更新用户
      this.axios
        .get(
          "http://192.168.0.125:3000/updateUser?id=" +
            this.row.id +
            "&username=" +
            this.row.username +
            "&userpass=" +
            this.row.userpass
        )
        .then(res => {
          if (res.data.code != 200) {
            return;
          }
          console.log(res.data.message);
          this.row = {}; //清楚当前行数据
          this.getList(); //获取用户列表
        });
    },
    cancel() {
      this.mask = false; //关闭弹框
      this.row = {}; //清楚当前行数据
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.home {
  padding-top: 30px;
  box-sizing: border-box;
}
.regist,
.refresh {
  margin: auto;
  width: max-content;
}
.refresh {
  margin: 10px auto;
}
table {
  border-collapse: collapse;
  border-color: rgb(206, 200, 200);
}
.tableCon {
  height: 500px;
  overflow-y: auto;
  margin: auto;
}
.tableCon caption {
  /* border: 1px solid;
  border-bottom: none; */
  padding: 6px 0;
  font-weight: bold;
}
.mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.4);
  z-index: 10;
}
.dialog {
  min-width: 400px;
  /* min-height: 300px; */
  background: white;
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  z-index: 11;
}
.dialog > .title {
  height: 42px;
  line-height: 42px;
  font-size: 16px;
  font-weight: bold;
  text-align: center;
  border-bottom: 1px solid rgb(206, 200, 200);
  box-sizing: border-box;
}
.dialog > .con {
  width: 100%;
  margin: 10px 0;
}
.dialog > .con > table > tr {
  height: 50px;
}
.dialog > .con input {
  width: 80px;
  height: 20px;
  border: none;
  outline: none;
  text-align: center;
}
.dialog > .oparate {
  text-align: right;
  border-top: 1px solid rgb(206, 200, 200);
  padding: 12px 40px;
  box-sizing: border-box;
}
@media screen and (max-width: 400px) {
  #app > .home {
    padding-top: 0;
    display: flex;
    flex-direction: column;
  }
  .tableCon {
    flex: 1;
    overflow-x: hidden;
    overflow-y: auto;
    box-shadow: inset 0 0 5px grey;
    border-radius: 10px;
    width: 96%;
  }
  table tr > td:last-child {
    text-align: center;
  }
  .regist {
    display: flex;
    flex-direction: column;
  }
  .regist > * {
    margin-bottom: 8px;
  }
}
</style>
