<template>
  <div class="header">
    <h1>Link</h1>
    <p class="score">得分 :
      <transition :name="score.type">
        <span v-if="change" :key="1">{{score.newValue}}</span>
        <span v-else :key="2">{{score.newValue}}</span>
      </transition>
    </p>
    <p class="high">最高分：
      <transition>
        <span v-if="changeHighScore" :key="3">{{high.newValue}}</span>
        <span v-else :key="4">{{high.newValue}}</span>
      </transition>
    </p>
    <p :class="color">{{time}}</p>
  </div>
</template>

<script>
    export default {
        name: "myHeader",
        props: ['change', 'score', 'time', 'changeHigh'],
        data: function () {
          return{
            color: {
              grey: false,
              orange: true,
              red: false
            },
            high: {
              oldValue: 0,
              newValue: 0
            },
            changeHighScore: false
          }
        },
        watch: {
          time: function () {
            if (this.time <= 10) {
              if (this.time <= 5) {
                this.color.red = true;
              } else {
                this.color.red = false;
                this.color.orange = true;
              }
            } else {
              this.color.red = false;
              this.color.orange = false;
              this.color.grey = true;
            }
          },
          changeHigh: function () {
            this.high.newValue = this.high.oldValue;
          }

        },
      created(){
          this.high.oldValue = localStorage.getItem("highScore") ? localStorage.getItem("highScore") : 0;
      }
    }
</script>

<style scoped>
  @keyframes biger {
    0% {
      transform: scale(1.2);
    }
    50% {
      transform: scale(1);
    }
    100% {
      transform: scale(1.2);
    }
  }
  .add-enter-active, .add-leave-active, .reduce-enter-active, .reduce-leave-active{
    transition: all 0.5s;
  }
  .add-enter {
    opacity: 0;
    transform: translateY(20px);
  }
  .add-leave-to {
    opacity: 0;
    transform: translateY(-20px);
  }
  .reduce-enter {
    opacity: 0;
    transform: translateY(-20px);
  }
  .reduce-leave-to {
    opacity: 0;
    transform: translateY(20px);
  }
  .header {
    margin-top: 50px;
  }
  .header h1 {
    margin-bottom: 35px;
    font-family: Cambria;
    font-size: 90px;
    font-weight: lighter;
    color: #5e5d59;
  }
  .header .score {
    position: relative;
    font-family: "Microsoft JhengHei";
    font-size: 22px;
    margin-bottom: 30px;
  }
  .header .score span {
    position: absolute;
    left: 75px;
    bottom: -5px;
    color: #5e5d59;
    font-size: 40px;
    font-weight: bolder;
  }
  .header .high {
    position: relative;
    font-family: "Microsoft JhengHei";
    font-size: 17px;
  }
  .header .high span {
    position: absolute;
    left: 80px;
    bottom: -2px;
    color: #5e5d59;
    font-size: 20px;
    font-weight: bolder;
  }
  .grey {
    color: #5e5d59;
    font-size: 120px;
    position: absolute;
    right: 100px;
    top: 60px;
  }
  .orange {
    color: #ff9968;
    font-size: 120px;
    position: absolute;
    right: 100px;
    top: 60px;
  }
  .red {
    animation: biger 1s infinite alternate;
    color: #ff4b41;
    font-size: 120px;
    position: absolute;
    right: 100px;
    top: 60px;
  }
</style>
