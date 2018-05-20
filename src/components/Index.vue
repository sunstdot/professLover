<template>
    <main class="main">
        <div class="header">
            <div class="logo"></div>
            <div class="title">
                <div class="title1"><a href="#" class="atext"> 首页</a></div>
            </div>
        </div>
        <section class="section">
            <div class="maintext">
                <h1 class="htext">在区块链上说出你的爱</h1>
                <h1 class="htext">让浪漫永远留存， 真情永不消失</h1>
            </div>
            <div class="mainsearch"><input class="maininput inputparent" placeholder="输入告白ID，查询你的爱" v-model="searchWord" /><div class="search" @click="dosearch"></div></div>
            <div class="loveBtn" @click="showLetter"></div>
        </section>
        <modal v-if="showModal" @close="showModal = false">
            <div class="modal-mask">
                <div class="modal-wrapper">
                    <div class="modal-container">
                        <div class="close" @click="closeLetter"></div>
                        <div class="from"><input class="frominput " placeholder="to" v-model="letterTo"/></div>
                        <div class="lettertext"><textarea class="textarea1" v-model="lettertext" placeholder="写下表白吧"></textarea></div>
                        <div class="to"><input class="frominput" placeholder="from" v-model="letterFrom"/></div>
                        <div class="send" @click="showXinfeng"></div>
                    </div>
                </div>
            </div>
        </modal>
        <modal v-if="showModal1" @close="showModal1 = false">
            <div class="modal-mask1">
                <div class="modal-wrapper1">
                    <div class="modal-container1">
                        <div class="close1" @click="closeXinfeng"></div>
                        <span class="inputtext">{{loadtext}}</span><div class="isloaded" v-if="isloaded"></div>
                        <input class="accountfile fileparent" type="file" id="file" ref="myFiles" @change="previewFiles"/>
                        <input class="accountpwd" placeholder="输入密码" v-model="password" />
                        <div class="sendmail" @click="sendmail">邮寄</div>
                    </div>
                </div>
            </div>
        </modal>
        <modal v-if="showModal2" @close="showModal2 = false">
            <div class="modal-mask1">
                <div class="modal-wrapper1">
                    <div class="modal-container2">
                       {{sendtip}}
                    </div>
                </div>
            </div>
        </modal>

    </main>
</template>


<script>
import { $http } from "../../lib/fetch";
// require('../server.js');
import {savePromise, searchPromise} from "../server.js";
export default {
  name: "professlover",
  data() {
    return {
      showModal: false,
      showModal1: false,
      passport: '',
      password:'',
      loadtext:'请导入钱包',
      isloaded: false,
      showModal2: false,
      searchWord: '',
      lettertext: '',
      letterFrom: '',
      letterTo: '',
      sendtip: ''
    };
  },
  mounted() {
    // let account = createAccount();
    
  },
  methods: {
    showLetter() {
        this.showModal = true;
    },
    closeLetter() {
        this.showModal = false;
    },
    showXinfeng() {
      this.showModal = false;
      this.showModal1 = true;
    },
    dosearch() {
      if(!this.searchWord) {
        return;
      }
      let self = this;
      searchPromise(this.searchWord, (value) => {
        this.showModal = true;
        self.lettertext = JSON.parse(value.result).content;
      });
    },
    sendmail() {
      if(!this.password || !this.passport) {
        return;
      }
      let self = this;
      savePromise(JSON.parse(this.passport), this.password,[this.letterFrom, this.letterTo, this.lettertext],function(value) {
        if(value === "success") {
          self.sendtip = "发送成功！"
        }else {
          self.sendtip = "发送失败，账户余额不足"
        }
        setTimeout(() => {
            self.showModal2 = false;
          }, 2000)
      })
      self.showModal1 = false;
      self.showModal2 = true;
      this.sendtip = "正在发送中......"
      //有了账号密码去上传到区块链
    },
    previewFiles(event) {
      console.log('66666');
      let file = event.target.files[0];
      let reader = new FileReader();
      reader.onloadend = (evt) => {
        this.passport = evt.target.result;  //获取账户信息

      };
      this.loadtext = file.name;
      this.isloaded = true;
      reader.readAsText(file);

    },
    closeXinfeng() {
      this.showModal1 = false;
    }
  }
};
</script>

<style>
.main {
  width: 100%;
}
.header {
  width: 100%;
  height: 100px;
}
.logo {
  position: relative;
  float: left;
  width: 289px;
  height: 86px;
  background: url("../assets/title.png") no-repeat;
}
.title {
  position: relative;
  float: left;
  height: 86px;

  margin-left: 20px;
}
.title1 {
  position: relative;
  float: left;
  font-size: 24px;
  height: 100%;
  line-height: 86px;
  margin-left: 15px;
  color: #838b83;
  cursor: pointer;
}
.title2 {
  position: relative;
  float: left;
  font-size: 24px;
  height: 100%;
  line-height: 86px;
  margin-left: 15px;
  cursor: pointer;
  color: #8b2323;
}
.atext {
  color: #838b83;
}
.btext {
  color: #8b2323;
}
.section {
  /* border: 1px solid blue; */
  position: absolute;
  top: 50%;
  left: 56%;
  margin-top: -200px;
  margin-left: -300px;
  width: 600px;
  height: 400px;
}
.maintext {
  margin-left: 30px;
}
.htext {
  font-size: 32px;
}
.mainsearch {
  /* border:1px solid red; */
  position: absolute;
  /* /* left: 50%; */
  margin-left: 40px;
  margin-top: 10px;
  height: 30px;
  width: 450px;
  background: url("../assets/search.png") no-repeat;
  border: 1px solid black;
}

.loveBtn {
  position: absolute;
  margin-top: 70px;
  margin-left: 170px;
  width: 135px;
  height: 40px;
  background: url("../assets/biaobai.png") no-repeat center center;
  background-size: cover;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
}
.maininput {
  margin-left: 5px;
  height: 28px;
  line-height: 28px;
  font-size: 16px;
  font-weight: bolder;
  color: #4f4f4f;
  width: 400px;
  /* display: inline-block; */
  float: left;
  
}
.search {
    width: 34px;
    height: 30px;
    margin-left: 9px;
    display: inline-block;
    cursor: pointer;
}
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 375px;
    height: 500px;
  margin: 0px auto;
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
  background: url("../assets/letter.jpg");
  background-size: cover;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
.close {
    width: 35px;
    height: 35px;
    position: absolute;
    top: 10px;
    right: 10px;
    background: url("../assets/close.png") no-repeat center center;
    background-size: cover;
    -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
}
.close:hover, .close:active {
    background: url("../assets/close1.png") no-repeat center center;
    background-size: cover;
    -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
}

.lettertext {
   position: absolute;
    left: 50%;
    top: 50%;
    margin-top: -140px;
    margin-left: -140px;
    /* border: 1px solid black; */
    width: 280px;
    height: 280px;
}
.textarea1 {
  width:100%;
  height: 100%;
  outline:none;
  border-style: none;
   font-family: Gill Sans, sans-serif;
  font-size: 28px;
  background-color: transparent;
  overflow:hidden; resize:none;
}
.send {
  position: absolute;
  left: 50%;
  margin-left: -20px;
  bottom: 50px;
  width: 40px;
  height: 35px;
  background: url("../assets/send.png") no-repeat center center;
  background-size: cover;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  cursor: pointer;
}
.send:hover,.send:active {
  background: url("../assets/send1.png") no-repeat center center;
  background-size: cover;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
}

.modal-mask1 {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper1 {
  display: table-cell;
  vertical-align: middle;
}

.modal-container1 {
  width: 546px;
  height:350px;
  margin: 0px auto;
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
  background: url("../assets/xinfeng.jpg");
  background-size: cover;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
.accountfile, .inputtext {
  /* border: 1px solid red; */
  position: absolute;
  left: 150px;
  top: 100px;
  height: 40px;
  width: 280px;
  font-size: 24px;
  color: #8B8682;
  line-height: 40px;
  border: 1px solid	#CDC0B0;
  background-color: transparent;
}
.fileparent {
  cursor: pointer;
  opacity:0;
  filter:alpha(opacity=0);
}
.accountpwd {
  border: 1px solid blue;
  position: absolute;
  left: 150px;
  top: 160px;
  height: 40px;
  width: 280px;
  font-size: 24px;
  border: 1px solid	#CDC0B0;
  background-color: transparent;
}
.sendmail {
  background-color: #8B6914;
  width: 140px;
  height: 40px;
  position: absolute;
  border-radius: 20px;
  left: 210px;
  top: 260px;
  text-align: center;
  font-size: 28px;
  font-weight: bolder;
  color: snow;
  line-height: 40px;
  cursor: pointer;
}
.inputparent {
  border-style: none;
  background-color: transparent;
  outline: none;
}
.close1 {
  position: absolute;
  top:10px;
  right: 10px;
   width: 35px;
    height: 35px;
    background: url("../assets/close.png") no-repeat center center;
    background-size: cover;
    -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
}
.close1:hover, .close1:active {
    background: url("../assets/close1.png") no-repeat center center;
    background-size: cover;
    -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
}
.isloaded {
  top: 103px;
  left: 393px;
  position: absolute;
  width: 35px;
  height: 35px;
  background: url("../assets/downloaded.png") no-repeat center center;
   background-size: cover;
    -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
}
.modal-container2 {
  width: 546px;
  height:350px;
  margin: 0px auto;
  transition: all 0.3s ease;
  text-align: center;
  line-height: 350px;
  font-weight: 900;
  color: #838B8B;
  font-size: 48px;
  font-family: Helvetica, Arial, sans-serif;
  background: url("../assets/xinfeng.jpg");
  background-size: cover;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover;
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
.from {
  position: absolute;
  top: 70px;
  left: 50px;
  width: 100px;
  height: 30px;
  /* border: 1px solid red; */
  /* background: url("../assets/line.png") repeat; */
  background-size: cover;
}
.frominput {
  height: 100%;
  width: 100%;
  border-color: #EED5B7; 
border-style: solid; 
border-top-width: 0px;
 border-right-width: 0px; 
border-bottom-width: 1px;
 border-left-width: 0px;
 font-size: 24px;
 text-align: left;
 line-height: 30px;
 font-weight: bold;
 color:#4f4f4f;
 background-color: transparent;
}
.to {
  position: absolute;
  bottom: 80px;
  right: 60px;
  width: 100px;
  height: 30px;
  /* border: 1px solid black; */
  /* background: url("../assets/line.png") repeat-x; */
  background-size: cover;
}
</style>