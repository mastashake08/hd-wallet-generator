<template>
  <div class="hello">
  <h2>{{mnemonic}}</h2>
    <p>
      BTC: {{btcAddr}}
    </p>
    <p>
    ETH: {{ethAddr}}
    </p>


  </div>
</template>

<script>
const bitcoin = require('bitcoinjs-lib')
const ethUtil = require('ethereumjs-util')
export default {
  name: 'HelloWorld',
  data () {
    return {
      mnemonic: '',
      seed: '',
      root: '',
      btcAddr: '',
      ethAddr: ''
    }
  },
  async created () {
    var bip39 = require('bip39')
    var hdkey = require('hdkey')
    this.mnemonic = bip39.generateMnemonic()
    this.seed = await bip39.mnemonicToSeed(this.mnemonic)
    this.root = hdkey.fromMasterSeed(this.seed)
    this.generateBTCAddress()
    this.generateETHAddress()
  },
  methods: {
    generateBTCAddress () {
      const network = bitcoin.networks.mainnet
      const path = "m/44'/0'/0'/0/0"
      var hdMaster = bitcoin.bip32.fromSeed(this.seed, network)
      const key = hdMaster.derivePath(path)
      const address = bitcoin.payments.p2pkh({
        pubkey: key.publicKey,
        network: network
      }).address
      this.btcAddr = address
    },
    generateETHAddress (){
      const path = "m/44'/60'/0'/0/0"
      const addrNode = this.root.derive(path)
      const privKey = addrNode._privateKey
      const pubKey = ethUtil.privateToPublic(privKey)
      const addr = ethUtil.publicToAddress(pubKey).toString('hex')
      this.ethAddr = ethUtil.toChecksumAddress('0x'+addr)

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
