<template>
  <div class="hello pt-5">
    <!-- <h1>Hi</h1> -->
    <!-- <pre>{{ entropyString }}</pre> -->
    <!-- <pre>{{ hash }}</pre> -->
    <div v-if="account !== ''" class="alert alert-warning text-center h6 mb-4">
      <b>TEST YOUR ACCOUNT FIRST! DEPOSIT AND TRY SENDING SOMETHING BEFORE USING YOUR ACCOUNT FOR LARGER AMOUNTS.</b>
      <br />
      Please do not try to copy/paste the generated account address and secret. Please write it down, double check and test the account first.
    </div>
    <div class="text-center">
      <b>Account address (public)</b>
      <br />
      <code class="generated font-weight-bold px-2 text-primary d-inline-block mb-3" v-clipboard:copy="'Account: ' + account">{{ account }}</code>
      <br />
      <b>Account secret (for your eyes only)</b>
      <br />
      <code class="generated font-weight-bold px-2 text-danger d-inline-block mb-3" v-clipboard:copy="'Secret (keep safe!): ' + secret">{{ secret }}</code>
    </div>
    <div class="mt-4 text-center">
      <button @click="print()" class="font-weight-bold btn btn-lg btn-dark">Print this (unsafe!) <small>- Better write down &amp; test</small></button>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import VueClipboard from 'vue-clipboard2'
import Crypto from 'crypto'
import XrplKeyPairs from 'ripple-keypairs'

Vue.use(VueClipboard)

let entropyCalculatorTimer

export default {
  name: 'Generate',
  props: {
    entropyString: String
  },
  methods: {
    print () {
      window.print()
    }
  },
  watch: {
    entropyString () {
      clearTimeout(entropyCalculatorTimer)
      entropyCalculatorTimer = setTimeout(() => {
        this.secret = XrplKeyPairs.generateSeed({ entropy: this.hash })
        const pair = XrplKeyPairs.deriveKeypair(this.secret)
        this.account = XrplKeyPairs.deriveAddress(pair.publicKey)
      }, 250)
    }
  },
  data () {
    return {
      secret: 'XXXXXXXXXXXXXXXXXXXXXXXXXXX',
      account: 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
    }
  },
  computed: {
    hash () {
      return Crypto.createHash('sha1').update(
        this.entropyString
      ).digest('hex').toUpperCase()
    }
  }
}
</script>

<style scoped lang="scss">
  code {
    letter-spacing: .1em;
    font-size: 2em;
    &:active {
      color: white !important;
      background-color: rgba(0, 0, 0, .8);
      outline: 0;
      box-shadow: 0 0 0 3px rgba(0, 123, 255, .5);
      border-radius: 4px;
      &:before {
        content: 'Copied! (Unsafe!)';
        display: block;
        position: absolute;
        color: black;
        font-size: 12px;
        margin-top: -22px;
        margin-left: -8px;
        letter-spacing: normal;
      }
    }
  }
</style>
