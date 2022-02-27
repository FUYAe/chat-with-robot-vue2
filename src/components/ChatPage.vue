<template>
  <div class="main">
    <!-- 头部图标 -->
    <div class="m-header">
      <img src="../assets/chat.png" height="15px" width="15px" alt />
      机器人聊天
    </div>
    <!-- 聊天内容 -->
    <div class="chat-container">
      <div class="chat-item" v-for="item in msgListReturn" :key="item.id" :class="item.style">
        <div>
          {{ item.msg }}
          <p class="time">{{ item.createtime }}</p>
        </div>
      </div>
    </div>
    <!-- 输入框 -->
    <div class="input">
      <textarea class="text" @keyup.enter="sendmsg" v-model="sendedText" cols="30" rows="10"></textarea>

      <input class="button" @click="sendmsg" value="发送" type="button" />
    </div>
  </div>
</template>
<script>
import axios from "axios";
import { nanoid } from "nanoid";
export default {
  data() {
    return {
      sendedText: "",
      respText: "",
      msgList: [],
    };

  },
  computed: {
    msgListReturn() {
      return this.msgList;
    },
  },
  watch: {
    deep: true,
    msgList: function () {
      // 输入框滚动条自动到底部
      this.$nextTick(() => {
        var el = document.querySelector(".chat-container")
        this.$nextTick(() => {
          el.scrollTop = el.scrollHeight - el.clientHeight;

        })
      })
    }
  },
  methods: {
    // 获取时间
    ct() {
      var now = new Date();
      var cy = now.getFullYear();
      var cm = now.getMonth() + 1;
      var cd = now.getDate();
      var ch = now.getHours();
      var cmin = now.getMinutes();
      var cs = now.getSeconds();
      return cy + "-" + cm + "-" + cd + " " + ch + ":" + cmin + ":" + cs;
    },
    sendmsg() {

      if (this.sendedText == "") return //没有输入内容不执行

      if (
        this.msgList.length == 0 ||
        this.msgList[this.msgList.length - 1].who == "ro" //机器人回复是逐条到，防止重复输入
      ) {
        this.msgList.push({
          id: nanoid(),
          who: "person",
          msg: this.sendedText,
          createtime: this.ct(),
          style: {
            right: true,
            left: false,
          },
        });

        axios
          .post("http://localhost:8000/send", {
            msg: this.sendedText,
          })
          .then((res) => {
            setTimeout(() => {
              this.msgList.push({
                id: nanoid(),
                who: "ro",
                msg: res.data.msg,
                createtime: res.data.time,
                style: {
                  right: false,
                  left: true,
                },
              });
            }, 500);
          })
          .catch((err) => {
            setTimeout(() => {
              this.msgList.push({
                id: nanoid(),
                who: "ro",
                msg: "无服务",
                createtime: "1999-1-1 00:00:00",
                style: {
                  right: false,
                  left: true,
                },
              });
            }, 500);
          });
      }

      this.sendedText = ""//发送后输入框为空

    },
  },
};
</script>
<style scoped>
.main {
  width: 800px;
  height: 800px;
  margin: 0 auto;
  background-color: rgb(255, 255, 255);
  border-radius: 10px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  padding: 20px;
}
.chat-container {
  padding-top: 15px;
  height: 60%;
  width: 100%;
  background-color: rgb(243, 243, 243);
  border-radius: 10px;
  margin-top: 10px;
  padding-left: 25px;
  padding-right: 25px;
  box-sizing: border-box;
  overflow-y: auto;
  scrollbar-width: none;
  padding-bottom: 30px;
  transition: 2s all ease;
}
::-webkit-scrollbar {
  display: none; /* Chrome Safari */
}
.input {
  background-color: rgb(255, 255, 255);
  height: 30%;
  margin-top: 20px;
  box-sizing: border-box;
  border-radius: 10px;
  width: 100%;
}
.input .text {
  width: 100%;
  height: 80%;
  background-color: rgb(233, 233, 233);
  border: none;
  border-radius: 10px;
  /* text-align: center; */
  font-size: 20px;
  color: rgb(85, 85, 85);
  padding: 15px;
  box-sizing: border-box;
  resize: none;
}
.input .text:focus {
  border: none;
  outline: none;
}
.input .button {
  width: 100%;
  height: 15%;
  background-color: rgb(96, 182, 107);
  border: none;
  margin-top: 10px;
  border-radius: 10px;
  color: rgb(59, 59, 59);
}
.input .button:hover {
  color: #000;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}
.left,
.right {
  height: 80px;
  width: 100%;
  margin: 1px;
}
.chat-item {
  transition: 2s all ease;
}
.left > div,
.right > div {
  position: relative;
  transition: 0.9s all ease;
  box-sizing: border-box;
  width: 49%;
  height: 90%;
  padding: 10px;
  border-radius: 9px;
  margin-top: 3px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
}
.left > div {
  float: left;
  /* text-align: center; */
  /* margin-left: 10px; */
  background-color: rgb(230, 230, 230);
}
.right > div {
  background-color: rgb(219, 219, 219);
  float: right;
  /* margin-right: 10px; */
  /* text-align: center; */
}
.time {
  color: rgb(124, 124, 124);
  font-size: 9px;
  position: absolute;
  top: 42px;
}
</style>
