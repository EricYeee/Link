<template>
  <div id="game">
    <div class="cell-container">
      <transition-group name="items-list" tag="div" class="items-container">
        <cell v-for="(item, index) in itemsData" @choose="add" :key="item.id" :content="item.num" :index="index" :side="item.side" :length="length" :clearClick="clearClick" ref="cells"></cell>
      </transition-group>
    </div>
    <transition  name="shade">
      <div class="shade" v-if="shade">
        <img src="../images/shade.png"/>
        <p class="title" id="over" v-if="over">GAME OVER</p>
        <p class="title" v-else>LINK!</p>
        <span class="button" :class="continueClass" id="continue" @click="toggleShade">继续</span>
        <span class="button" id="start" @click="start">开始</span>
      </div>
    </transition>
    <span class="button" id="change" @click="useChange" :class="buttonClass">
      使用随机 : {{changeNum}}
    </span>
    <span class="button" id="stop" :class="shadeClass" @click="stop">暂停</span>
  </div>
</template>

<script>
    import _ from 'lodash';
    import Cell from "./cell";

    export default {
      components: {Cell},
      name: "game",
        props: ['items', 'over', 'clearFocus', 'changeNum'],
        data: function () {
          return{
            itemsData: [],
            focus: [],
            exist: [],
            spreadList: [],
            buttonClass: {
              changeFb: false
            },
            shade: false,
            shadeClass: {
              shadeFb: false
            },
            firstTime: true,
            continueClass: {
              continueFb: false
            },
            clearClick: false
          }
        },
        computed: {
          length: function () {
            return this.focus.length;
          }
        },
        methods: {
          shuffle: function () {
            this.itemsData = _.shuffle(this.itemsData);
            for (let i=0; i<100; i++) {
              if (((i >= 0) && (i <= 9)) || ((i >= 90) && (i <= 99)) || (i % 10) == 9 || (i % 10) == 0) {
                if (this.itemsData[i].num !== null) {
                  this.itemsData[i].side = true;
                }
              } else {
                this.itemsData[i].side = false;
              }
            }
          },
          toggleShade: function () {
            if (!this.continueClass.continueFb) {
              this.shade = !this.shade;
            }
          },
          start: function () {
            if (this.firstTime) {
              this.firstTime = false;
              this.shade = !this.shade;
            } else {
              this.$emit("restart");
              this.shade = !this.shade;
              this.firstTime = false;
              this.clearClick = !this.clearClick;
            }
          },
          stop: function () {
            if (!this.shadeClass.shadeFb) {
              this.shade = !this.shade;
            }
          },
          add: function (obj) {
            if (obj.add) {
              this.focus.push(obj);
            } else {
              this.focus.pop();
            }
          },
          useChange: function () {
            if (this.changeNum > 0 && !this.buttonClass.changeFb) {
              this.clearClick = !this.clearClick;
              this.focus = [];
              this.shuffle();
              this.$emit('useChangeTime');
            }
          },
          check: function (pt1, pt2) {
            let obj = {};
            obj.top = (pt1 - 10) >= 0 ? (pt1 - 10) : -1;
            obj.bottom = (pt1 + 10) <= 99 ? (pt1 + 10) : -1;
            obj.left = (pt1 % 10) !== 0 ? (pt1 - 1) : -1;
            obj.right = ((pt1 + 1) % 10) !== 0 ? (pt1 + 1) : -1;
            let result = false;
            Object.keys(obj).forEach(function (value) {
              if (this.exist.indexOf(obj[value]) !== -1) {
                obj[value] = -1;
              }
              if (obj[value] == pt2) {
                result = true;
              }
            }, this)
            if (result) {
              return true;
            } else {
              let result2 = false;
              Object.keys(obj).forEach(function (value) {
                if (obj[value] >= 0 && this.itemsData[obj[value]].num == null) {
                  if (this.exist.indexOf(pt1) == -1) {
                    this.exist.push(pt1);
                  }
                  if (this.check(obj[value], pt2)) {
                    result2 = true;
                  }
                }
              }, this)
              return result2;
            }
          },
          spread: function (index) {
            if (this.itemsData[index].side == true) {
              let obj = {};
              obj.top = (index - 10) >= 0 ? (index - 10) : -1;
              obj.bottom = (index + 10) <= 99 ? (index + 10) : -1;
              obj.left = (index % 10) !== 0 ? (index - 1) : -1;
              obj.right = ((index + 1) % 10) !== 0 ? (index + 1) : -1;
              Object.keys(obj).forEach(function (value) {
                let position = obj[value];
                if (position >= 0) {
                  if (!this.itemsData[position].side) {
                    this.itemsData[position].side = true;
                  }
                  if (this.itemsData[position].num == null && (this.spreadList.indexOf(position) == -1)) {
                    this.spreadList.push(position);
                    this.spread(position);
                  }
                }
              }, this)
            }
          },
          getPoint: function () {
            let index1 = this.focus[0].index;
            let index2 = this.focus[1].index;
            this.itemsData[index1].num = null;
            this.itemsData[index2].num = null;
            this.focus.splice(0, 2);
            this.spreadList = [];
            this.spread(index1);
            this.spreadList = [];
            this.spread(index2);
            this.$emit('add');
          },
          losePoint: function () {
            this.focus.splice(0, 2);
            this.clearClick = !this.clearClick;
            this.$emit('reduce');
          }
        },
        watch: {
          focus: function () {
            let right = false;
            if (this.focus.length == 2){
              let sum = (this.focus[0].num + this.focus[1].num) % 10;
              if (!sum) {
                if (this.focus[0].side == true && this.focus[1].side == true) {
                  right = true;
                } else {
                  this.exist = [];
                  let result = this.check(this.focus[0].index, this.focus[1].index);
                  if (result) {
                    right = true;
                  }
                }
              } else {
                right = false;
              }
              if (right) {
                this.getPoint();
              } else {
                this.losePoint();
              }
            }
          },
          shade: function (newValue) {
            this.shadeClass.shadeFb = this.shade;
            if (!this.over) {
              if (this.firstTime) {
                this.continueClass.continueFb = true;
              } else {
                this.continueClass.continueFb = false;
              }
            }
            if (newValue) {
              this.buttonClass.changeFb = true;
              this.$emit('handlerTime', true);
            } else {
              this.buttonClass.changeFb = false;
              this.$emit('handlerTime', false);
            }
          },
          over: function (newValue) {
            if (newValue) {
              this.shade = true;
              this.continueClass.continueFb = true;
              this.$emit("savePoint");
            }
          },
          items: function (newValue) {
            this.itemsData = newValue;
            this.shuffle();
          },
          clearFocus: function () {
            this.focus = [];
          },
          changeNum: function () {
            if (this.changeNum <= 0) {
              this.buttonClass.changeFb = true;
            }
          }
        },
        created(){
          this.itemsData = this.items;
          this.shuffle();
          this.shade = true;
        }
    }
</script>

<style scoped>
  .items-list-move {
    transition: transform 1s;
  }
  .shade-enter-active, .shade-leave-active {
    transition: all 0.3s;
  }
  .shade-enter, .shade-leave-to {
    opacity: 0;
  }
  #game {
    position: relative;
    margin-top: 40px;
  }
  .shade {
    position: absolute;
    top: 0;
    width: 570px;
    height: 570px;

  }
  .shade img {
    width: 100%;
    height: 100%;
    opacity: 0.9;
    filter: blur(5px);
  }
  .button {
    position: absolute;
    display: inline-block;
    background-color: #ff7487;
    border-radius: 5px;
    text-align: center;
    color: white;
    font-family: "Microsoft JhengHei UI";
    font-size: 19px;
    font-weight: lighter;
    line-height: 2;
    cursor: pointer;
  }
  .shade span {
    width: 100px;
    height: 40px;
    top: 60%;
    transform: translateY(-50%);
  }
  .shadeFb {
    background-color: #898487;
    cursor: default;
  }
  .title {
    position: absolute;
    top: 27%;
    left: 29%;
    font-family: "Cambria";
    font-size: 100px;
    color: #5e5d59;
    font-weight: lighter;
  }
  #over {
    left: 15%;
    top: 31%;
    font-size: 75px;
  }
  #continue {
    left: 125px;
  }
  .continueFb {
    background-color: #898487;
    cursor: default;
  }
  #start {
    right: 125px;
  }
  #stop {
    right: 0;
    width: 100px;
    height: 40px;
    margin-top:30px;

  }
  .cell-container{
    overflow: hidden;
  }
  #change {
    right: 130px;
    margin-top:30px;
    width: 150px;
    height: 40px;
  }

  .changeFb {
    background-color: #898487;
    cursor: default;
  }
</style>
