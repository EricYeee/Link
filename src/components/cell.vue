<template>
  <div class="item" :class="toggle" @click="change">
    {{content}}
  </div>
</template>

<script>
    export default {
        name: "cell",
        props: ['content', 'index', 'length', 'side', 'clearClick'],
        data: function () {
          return{
            toggle: {
              isClick: false
            }
          }
        },
      methods: {
          change: function () {
            this.toggle.isClick = !this.toggle.isClick;
            let obj = new Object();
            obj.index = this.index;
            obj.num = this.content;
            obj.side = this.side;
            if (this.toggle.isClick){
              obj.add = true;
            } else {
              obj.add = false;
            }
            if (this.length < 2){
              this.$emit('choose', obj);
            }
          }
      },
      watch: {
          clearClick: function () {
            if (this.content !== null) {
              this.toggle.isClick = false;
            }
          }
      }
    }
</script>

<style scoped>
  @keyframes shade1 {
    0% {
      transform: rotate(10deg) scale(1.3);
    }
    12.5% {
      transform: rotate(7.5deg) scale(1.3);
    }
    25% {
      transform: rotate(5deg) scale(1.3);
    }
    37.5% {
      transform: rotate(2.5deg) scale(1.3);
    }
    50% {
      transform: rotate(0deg) scale(1.3);
    }
    62.5% {
      transform: rotate(-2.5deg) scale(1.3);
    }
    75% {
      transform: rotate(-5deg) scale(1.3);
    }
    87.5% {
      transform: rotate(-7.5deg) scale(1.3);
    }
    100% {
      transform: rotate(-10deg) scale(1.3);
    }
  }
  @keyframes shade2 {
    0% {
      transform: rotate(10deg) scale(1);
    }
    12.5% {
      transform: rotate(7.5deg) scale(1);
    }
    25% {
      transform: rotate(5deg) scale(1);
    }
    37.5% {
      transform: rotate(2.5deg) scale(1);
    }
    50% {
      transform: rotate(0deg) scale(1);
    }
    62.5% {
      transform: rotate(-2.5deg) scale(1);
    }
    75% {
      transform: rotate(-5deg) scale(1);
    }
    87.5% {
      transform: rotate(-7.5deg) scale(1);
    }
    100% {
      transform: rotate(-10deg) scale(1);
    }
  }
  .item {
    float: left;
    width: 55px;
    height: 55px;
    border: 1px dashed #ff7487;
    text-align: center;
    line-height: 2;
    font-size: 30px;
    font-weight: lighter;
    color: #ff7487;
    cursor: pointer;
  }
  .item:hover {
    border-color: transparent;
    color: #ffd24c;
    animation: shade1 0.2s infinite alternate;
  }
  .isClick {
    pointer-events: none;
    cursor: default;
    border-color: transparent;
    color: #ffd24c;
    animation: shade2 0.2s infinite alternate;
  }
</style>
