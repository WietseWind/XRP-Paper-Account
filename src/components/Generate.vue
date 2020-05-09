<template>
  <div class="hello pt-5">
    <!-- <h1>Hi</h1> -->
    <!-- <pre>{{ entropyString }}</pre> -->
    <!-- <pre>{{ hash }}</pre> -->
    <div v-if="account !== ''" class="alert alert-warning text-center h6 mb-4">
      <b>TEST YOUR ACCOUNT FIRST! DEPOSIT AND TRY SENDING SOMETHING BEFORE USING YOUR ACCOUNT FOR LARGER AMOUNTS.</b>
    </div>
    <div class="text-center">
      <b>Account address (public)</b>
      <code class="text-primary d-block mb-3"><b>{{ account }}</b></code>
      <b>Account secret (for your eyes only)</b>
      <code class="text-danger d-block mb-3"><b>{{ secret }}</b></code>
    </div>
    <div class="mt-4 text-center">
      <button @click="print()" class="font-weight-bold btn btn-lg btn-dark">Print this</button>
    </div>
  </div>
</template>

<script>
import Crypto from 'crypto'
import XrplKeyPairs from 'ripple-keypairs'

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
  }
</style>
