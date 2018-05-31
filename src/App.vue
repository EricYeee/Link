<template>
  <div id="container">
    <myHeader :score="score" :change="change" :time="time" :changeHigh="changeHigh"></myHeader>
    <game :items="items" :over="over" :clearFocus="clearFocus" :changeNum="changeNum" @add="addScore" @reduce="reduceScore" @handlerTime="handlerTime" @restart="restart" @useChangeTime="useChangeTime" @savePoint="savePoint"></game>
    <myFooter></myFooter>
  </div>
</template>

<script>

import myHeader from "./components/myHeader";
import Game from "./components/game";
import myFooter from "./components/myFooter";

export default {
  name: 'App',
  components: {
    myHeader,
    Game,
    myFooter
  },
  data: function () {
    return{
      items:[],
      score: {
        newValue: 0,
        oldValue: 0,
        type: "add"
      },
      change: false,
      time: 10,
      timeout: null,
      over: false,
      clearFocus: false,
      changeNum: 2
    }
  },
  methods: {
    init: function () {
      this.items = [];
      let id = 1;
      for (let i=1; i<=10; i++){
        for (let j=1; j<=10; j++){
          let obj = new Object();
          obj.num = j;
          obj.id = id++;
          obj.side = false;
          this.items.push(obj);
        }
      }
    },
    addScore: function () {
      clearInterval(this.timeout);
      this.time = this.time + 9;
      this.timeout = setInterval(function () {
        this.time = this.time - 1;
      }.bind(this), 1000);

      this.score.oldValue = this.score.newValue;
      this.score.newValue = this.score.newValue + 10;
      this.score.type = "add";
      this.change = !this.change;
    },
    reduceScore: function () {
      this.score.oldValue = this.score.newValue;
      this.score.newValue = this.score.newValue - 10;
      this.score.type = "reduce";
      this.change = !this.change;
    },
    handlerTime: function (clear) {
      if (clear) {
        clearInterval(this.timeout);
      } else {
        this.timeout = setInterval(function () {
          this.time = this.time - 1;
        }.bind(this), 1000);
      }
    },
    restart: function () {
      this.init();
      this.time = 10;
      if (this.score.newValue !== 0) {
        if (this.score.newValue < 0) {
          this.score.type = "add";
        } else {
          this.score.type = "reduce";
        }
        this.score.oldValue = this.score.newValue;
        this.score.newValue = 0;
        this.change = !this.change;
      }
      this.changeNum = 2;
      this.over = false;
      this.clearFocus = !this.clearFocus;
    },
    useChangeTime: function () {
      this.changeNum--;
    },
    savePoint: function () {
      let oldValue = localStorage.getItem("highScore") ? localStorage.getItem("highScore") : 0;
      if (this.score.newValue > oldValue) {
        alert("ok");
        localStorage.setItem("highScore", this.score.newValue);
        alert(localStorage.getItem("highScore"));
        this.changeHigh = !this.changeHigh;
      }
    }
  },
  watch: {
    time: function (newValue) {
      if (newValue <= 0) {
        clearInterval(this.timeout);
        this.over = true;
      }
    }
  },
  created(){
    this.init();
    this.timeout = setInterval(function () {
      this.time = this.time - 1;
    }.bind(this), 1000);
  }
}
</script>

<style>
  @import "./style/reset.css";
  #container {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    width: 570px;
    height: 1500px;
    background-color: white;
    overflow: hidden;
    padding: 0 80px;
    box-shadow: 2px 5px 10px #888888;
  }
  html {
    background: url('./images/background.png') repeat;
  }
</style>
