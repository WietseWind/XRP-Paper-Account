<template>
  <div class="hello pt-2">
    <!-- <h1>Hi</h1> -->
    <!-- <pre>{{ entropyString }}</pre> -->
    <!-- <pre>{{ hash }}</pre> -->
    <div v-if="account !== ''" class="alert alert-warning text-center h6 mb-3">
      <b>TEST YOUR ACCOUNT FIRST! DEPOSIT AND TRY SENDING SOMETHING BEFORE USING YOUR ACCOUNT FOR LARGER AMOUNTS.</b>
      <br />
      Please do not try to copy/paste the generated account address and secret. Please write it down, double check and test the account first.
    </div>
    <div class="text-center">
      <b>Account address (public)</b>
      <br />
      <code class="generated font-weight-bold px-2 text-primary d-inline-block mb-2" v-clipboard:copy="'Account: ' + account">{{ account }}</code>
      <br />
      <b>Account secret (for your eyes only)</b>
      <br />
      <code class="generated font-weight-bold px-2 text-danger d-inline-block mb-2" v-clipboard:copy="'Secret Numbers (keep safe!): ' + secret.join(' ')">
        <small><small class="text-muted">A</small></small>{{ secret[0] }}
        <small><small class="text-muted">B</small></small>{{ secret[1] }}
        <small><small class="text-muted">C</small></small>{{ secret[2] }}
        <small><small class="text-muted">D</small></small>{{ secret[3] }}<br />
        <small><small class="text-muted">E</small></small>{{ secret[4] }}
        <small><small class="text-muted">F</small></small>{{ secret[5] }}
        <small><small class="text-muted">G</small></small>{{ secret[6] }}
        <small><small class="text-muted">H</small></small>{{ secret[7] }}
      </code>
      <br />
      <small>
        - or - <b>Family Seed</b> (Alternative but common secret format)
        <br />
        <code class="generated px-2 text-danger d-inline-block mb-2" v-clipboard:copy="'Family Seed Secret (keep safe!): ' + familySeed">
          {{ familySeed }}
        </code>
      </small>
    </div>
    <div class="mt-4 text-center d-print-none">
      <button @click="print()" class="font-weight-bold btn btn btn-dark">Print this (unsafe!) <small>- Better write down &amp; test</small></button>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import VueClipboard from 'vue-clipboard2'
import Crypto from 'crypto'
// import XrplKeyPairs from 'ripple-keypairs'
import { Account } from 'xrpl-secret-numbers'

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
        const account = new Account(Buffer.from(this.hash.slice(0, 32), 'hex'))
        this.secret = account.secret
        this.account = account.account.address
        this.familySeed = account.account.familySeed
      }, 250)
    }
  },
  data () {
    return {
      account: 'XXXXXXXXXXXXXXXXXXXXXXXXXXX',
      secret: ['XXXXXX', 'XXXXXX', 'XXXXXX', 'XXXXXX', 'XXXXXX', 'XXXXXX', 'XXXXXX', 'XXXXXX'],
      familySeed: 'XXXXXXXXXXXXXXXXXXXXXXXXXXX'
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
    font-size: 1.5em;
    &:active {
      color: white !important;
      background-color: rgba(0, 0, 0, .8);
      outline: 0;
      box-shadow: 0 0 0 3px rgba(0, 123, 255, .5);
      border-radius: 4px;
      z-index: 10;
      position: relative;
      &:before {
        content: 'Copied! (Unsafe!)';
        background-color: rgba(200, 0, 0, .9);
        padding: 1px 4px;
        border-radius: 4px;
        display: block;
        position: absolute;
        color: white;
        font-weight: bold;
        border: 1px solid rgba(0, 0, 0, .5);
        font-size: 12px;
        margin-top: -28px;
        margin-left: -10px;
        letter-spacing: normal;
      }
    }
  }
</style>
