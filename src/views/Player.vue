<template>
  <div class="player" ref="player" @mouseenter="enter" @mouseleave="leave">
    <div class="bar" v-show="show">
      <ICClose class="icon" @click="close"></ICClose>
      <ICMinimize class="icon" @click="minimize"></ICMinimize>
      <ICUnPin class="icon" v-show="pin" @click="toggle"></ICUnPin>
      <ICPin class="icon" v-show="!pin" @click="toggle"></ICPin>
    </div>
  </div>
</template>
<script>
import Chimee from 'chimee'
import ChimeeKernelHls from 'chimee-kernel-hls'
import { ipcRenderer } from 'electron'
import ICPin from '@/assets/images/ic_pin.svg'
import ICUnPin from '@/assets/images/ic_unpin.svg'
import ICClose from '@/assets/images/ic_player_close.svg'
import ICMinimize from '@/assets/images/ic_minimize.svg'
export default {
  data: function() {
    return {
      show: false,
      pin: false
    }
  },
  components: {
    ICPin,
    ICUnPin,
    ICClose,
    ICMinimize
  },
  watch: {
    pin(newPin) {
      localStorage.pinTop = newPin
    }
  },
  methods: {
    toggle: function() {
      this.pin = !this.pin
      ipcRenderer.send('pinToggle', this.pin)
    },
    close: function() {
      ipcRenderer.send('closePlayer', this.pin)
    },
    minimize: function() {
      ipcRenderer.send('minimizePlayer', this.pin)
    },
    enter: function() {
      this.show = true
    },
    leave: function() {
      this.show = false
    }
  },
  mounted() {
    this.pin = localStorage.pinTop === 'true'
    let args = this.$route.query
    let player = this.$refs.player
    let chimee = new Chimee({
      wrapper: player,
      isLive: true,
      kernels: {
        hls: ChimeeKernelHls
      },
      crossOrigin: true,
      autoplay: true,
      autoload: false,
      poster: args.thumb,
      controls: true
    })
    chimee.load(args.url)
    chimee.play()
  }
}
</script>
<style lang="scss" scoped>
.player {
  width: 100%;
  height: 100%;
  background: black;
  border-radius: 5px;
  color: #fff;
  .bar {
    width: 100%;
    position: absolute;
    right: 0;
    padding-top: 12px;
    padding-bottom: 12px;
    z-index: 10;
    display: flex;
    flex-direction: row-reverse;
    background: linear-gradient(0deg, rgba(0, 0, 0, 0) 0%, rgba(0, 0, 0, 0.5) 50%, rgba(0, 0, 0, 0.8) 100%);
    .icon {
      margin-right: 12px;
    }
  }
}
</style>
