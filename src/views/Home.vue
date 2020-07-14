<template>
  <div id="home">
    <h1>{{ currentText }}</h1>
    <h2>{{ timetext }}</h2>
    <div :style="{animationPlayState:cstyle}" id="all">
      <div id="a"></div>
      <div id="b"></div>
      <div id="c"></div>
      <div id="d"></div>
    </div>
    <b-btn variant="secondary" v-if="status != 1" @click="start">
      <font-awesome-icon :icon="['fas', 'play']"></font-awesome-icon>
    </b-btn>
    <b-btn variant="info" v-if="status == 1" @click="pause">
      <font-awesome-icon :icon="['fas', 'pause']"></font-awesome-icon>
    </b-btn>
    <b-btn variant="info" v-if="current.length > 0 || todos.length > 0" @click="finish(true)">
      <font-awesome-icon :icon="['fas', 'step-forward']"></font-awesome-icon>
    </b-btn>
  </div>
</template>

<script>
export default {
  data () {
    return {
      // 0 = 停止
      // 1 = 播放
      // 2 = 暫停
      status: 0,
      timer: 0
    }
  },
  computed: {
    currentText () {
      return this.current.length > 0 ? this.current : this.todos.length > 0 ? '點擊開始' : '沒有待做清單'
    },
    timetext () {
      const m = Math.floor(this.timeleft / 60)
      const s = Math.floor(this.timeleft % 60)
      return `${m} : ${s}`
    },
    alarm () {
      return this.$store.getters.alarm
    },
    timeleft () {
      return this.$store.getters.timeleft
    },
    current () {
      return this.$store.getters.current
    },
    todos () {
      return this.$store.getters.todos
    },
    cstyle () {
      return (this.status === 1) ? 'running' : 'paused'
    }
  },
  methods: {
    start () {
      if (this.status === 2) {
        // 暫停後繼續
        this.status = 1
        this.timer = setInterval(() => {
          this.$store.commit('countdown')
          if (this.timeleft <= 0) {
            this.finish(false)
          }
        }, 1000)
      } else {
        // 開始新倒數
        if (this.todos.length > 0) {
          this.$store.commit('start')
          this.status = 1
          this.timer = setInterval(() => {
            this.$store.commit('countdown')
            if (this.timeleft <= 0) {
              this.finish(false)
            }
          }, 1000)
        }
      }
    },
    finish (skip) {
      clearInterval(this.timer)
      this.status = 0
      this.$store.commit('finish')

      if (!skip) {
        const audio = new Audio()
        audio.src = './alarms/' + this.alarm
        audio.play()
      }

      if (this.todos.length > 0) {
        this.start()
      } else {
        alert('時間到')
      }
    },
    pause () {
      clearInterval(this.timer)
      this.status = 2
    }
  }
  // watch: {
  //   status: function () {
  //     if (this.status === 1) {
  //       this.cstyle = { transform: `rotate3d(${this.x},${this.x},${this.x},${this.x * 20}deg)` }
  //       this.x++
  //     }
  //   }
  // }
}
</script>
