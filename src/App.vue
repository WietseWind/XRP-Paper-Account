<template>
  <div id="app" class="container pt-2 pb-1">
    <code class="d-print-none text-muted inputData">{{ entropyInput.split('').reverse().join('').toUpperCase() }}</code>
    <i class="d-print-none" v-for="(k, i) in coords.split('&')" v-bind:key="i" :style="{left: k.split('.')[0] + 'px', top: k.split('.')[1] + 'px'}"></i>
    <div class="row h-100">
      <div class="col-12 h-100 d-print-none">
        <h3>
          <b>XRP Ledger Paper Account Generator</b>
        </h3>
      </div>
      <div class="d-block d-md-none col-12">
        <div class="alert alert-warning text-center mt-3">
          <b>This tool is not available on mobile clients.</b>
          <br />
          Please use your desktop.
        </div>
      </div>
      <div class="d-none d-md-block col-12 h-100">
        <div class="input-group input-group-lg mt-4">
          <input
            @focus="isTyping = true"
            @blur="isTyping = false"
            v-model="text"
            type="search"
            autocomplete="off"
            class="form-control d-print-none"
            name="text"
            placeholder="Type something..." />
        </div>
        <div class="progress mt-1 mb-2 d-print-none">
          <div class="progress-bar" :class="progressColor" role="progressbar" :style="{width: progress + '%'}" :ariaValuenow="progress" aria-valuemin="0" aria-valuemax="100"></div>
        </div>
        <!-- <pre>{{ isTyping }}</pre> -->
        <!-- <pre>{{ isDrawing }}</pre> -->
        <div v-if="progressColor === 'bg-danger'" class="alert alert-primary text-center h5 mt-5 py-3" style="z-index: -10">
          Please generate more random data by typing in the input box &amp; by painting with your mouse or by touching (tablet/touch PC)
        </div>
        <Generate :entropyString="entropyInput" v-if="progressColor !== 'bg-danger'" />

        <div class="mt-5 pt-3">
          <div class="text-center">
            By <a class="btn btn-primary btn-sm py-1" href="https://twitter.com/WietseWind" target="_blank"><b>@WietseWind</b></a>
            <span class="d-print-none">
              - <a class="btn btn-primary btn-sm py-1" href="https://github.com/WietseWind/XRP-Paper-Account" target="_blank"><b>Source code (Github)</b></a>
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Generate from './components/Generate.vue'
const initTime = String((new Date()) / 1)

export default {
  name: 'App',
  components: {
    Generate
  },
  data () {
    return {
      text: '',
      isDrawing: false,
      isTyping: false,
      currentCoords: { x: 0, y: 0 },
      lastCoords: { x: 0, y: 0 },
      coords: '-10.-10',
      log: '...',
      entropyInput: ''
    }
  },
  watch: {
    text () {
      this.calcEntropy()
    }
  },
  methods: {
    calcEntropy () {
      const width = String(window.innerWidth || document.documentElement.clientWidth || document.body.clientWidth)
      const height = String(window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight)
      const lang = navigator.language || navigator.userLanguage

      this.entropyInput = navigator.userAgent + ' ' + width + 'x' + height + ' ' + lang + ' ' + initTime + ' ' + this.text + ' ' + this.coords
    }
  },
  computed: {
    progress () {
      return Math.min(17 + this.entropyInput.length / 100 + this.text.trim().length / 4, 100)
    },
    progressColor () {
      if (this.progress < 60) {
        return 'bg-danger'
      }
      if (this.progress < 80) {
        return 'bg-warning'
      }
      return 'bg-success'
    }
  },
  mounted () {
    const noTarget = ['input', 'button', 'a', 'code']
    const start = e => {
      if (noTarget.indexOf(e.target.tagName.toLowerCase()) < 0) {
        this.isDrawing = true
        if (!this.isTyping) {
          document.querySelector('body').style.touchAction = 'none'
          e.preventDefault()
          return false
        }
      }
    }
    const stop = e => {
      document.querySelector('body').style.touchAction = 'auto'
      this.isDrawing = false
      this.calcEntropy()
    }
    const move = e => {
      if (this.isDrawing && !this.isTyping && noTarget.indexOf(e.target.tagName.toLowerCase()) < 0) {
        e.preventDefault()

        if (e.changedTouches) {
          for (let i = 0; i < e.changedTouches.length; i++) {
            // this.log += 'touchpoint[' + i + '].pageX = ' + e.changedTouches[i].pageX
            // this.log += 'touchpoint[' + i + '].pageY = ' + e.changedTouches[i].pageY
          }
        }

        const offset = 10

        this.currentCoords.x = e.pageX
        this.currentCoords.y = e.pageY
        if (
          Math.abs(this.currentCoords.x - this.lastCoords.x) >= offset ||
          Math.abs(this.currentCoords.y - this.lastCoords.y) >= offset
        ) {
          this.coords += ` & ${this.currentCoords.x}.${this.currentCoords.y}`
          Object.assign(this.lastCoords, this.currentCoords)
          this.calcEntropy()
        }
      }
    }

    window.addEventListener('mousedown', start)
    window.addEventListener('touchstart', start)

    window.addEventListener('mouseup', stop)
    window.addEventListener('touchend', stop)

    window.addEventListener('mousemove', move)
    window.addEventListener('touchmove', move)
  }
}
</script>

<style lang="scss">
  @media print {
    .d-print-none {
      display: none!important;
    }
  }
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
  }
  div.container, div.row {
    min-height: calc(100vh);
  }
  i {
    border: 4px solid green;
    border-radius: 20px;
    position: absolute;
    display: block;
    pointer-events: none;
    opacity: .15;
  }
  .inputData {
    position: absolute;
    left: 0; top: 0; right: 0; bottom: 0; z-index: -10;
    opacity: .12; font-size: .9em; padding: 10px;
  }
  html, body {
    cursor: pointer;
    overflow: hidden;
    -webkit-touch-callout: none; /* iOS Safari */
      -webkit-user-select: none; /* Safari */
      -khtml-user-select: none; /* Konqueror HTML */
        -moz-user-select: none; /* Old versions of Firefox */
          -ms-user-select: none; /* Internet Explorer/Edge */
              user-select: none; /* Non-prefixed version, currently
                                    supported by Chrome, Edge, Opera and Firefox */
  }
</style>
