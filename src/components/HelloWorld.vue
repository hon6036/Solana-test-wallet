<template>
  <div class="hello">
    <li>FromKey Balance:{{ fromkeybalance }}</li>
    <li>ToKey Balance: {{ tokeybalance }}</li>
    <button @click="reqAirdropFromKey">Airdrop To FromKey</button>
    <button @click="reqAirdropToKey">Airdrop To ToKey</button>
    <button @click="sendTransaction">SendTransaction</button>
  </div>
</template>

<script>
import {
  Connection,
  clusterApiUrl,
  Keypair,
  Transaction,
  LAMPORTS_PER_SOL,
  sendAndConfirmTransaction,
  SystemProgram,
  requestAirdrop
} from '@solana/web3.js'
const connection = new Connection(clusterApiUrl('testnet'))
const fromkeypair = Keypair.generate()
const tokeypair = Keypair.generate()
const fromkeybalance = 0
const tokeybalance = 0
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data () {
    return {
      fromkeybalance: fromkeybalance,
      tokeybalance: tokeybalance
    }
  },
  async mounted () {
    this.fromkeybalance = await connection.getBalance(fromkeypair.publicKey)
    this.tokeybalance = await connection.getBalance(tokeypair.publicKey)
  },
  methods: {
    async reqAirdropFromKey () {
      const fromAirdropSignature = await connection.requestAirdrop(
        fromkeypair.publicKey,
        1000000000
      )
      await connection.confirmTransaction(fromAirdropSignature)
      this.fromkeybalance = await connection.getBalance(fromkeypair.publicKey)
    },
    async reqAirdropToKey () {
      const fromAirdropSignature = await connection.requestAirdrop(
        tokeypair.publicKey,
        1000000000
      )
      await connection.confirmTransaction(fromAirdropSignature)
      this.tokeybalance = await connection.getBalance(tokeypair.publicKey)
      console.log(await connection.getBalance(tokeypair.publicKey))
    },
    async sendTransaction () {
      const transaction = new Transaction()
      transaction.add(
        SystemProgram.transfer({
          fromPubkey: fromkeypair.publicKey,
          toPubkey: tokeypair.publicKey,
          lamports: 500000000
        })
      )
      const signature = await sendAndConfirmTransaction(
        connection,
        transaction,
        [fromkeypair]
      )
      console.log(signature)
      this.fromkeybalance = await connection.getBalance(fromkeypair.publicKey)
      this.tokeybalance = await connection.getBalance(tokeypair.publicKey)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
