<template>
  <div>
    <div id="title">
      <img v-bind:src="require('./assets/qrypto_pretties.png')" width="400">
    </div>
    <div id="contents">
      <div>
        Token ID: {{$route.params.id}}<br />
      </div>
      <div>
        <img v-bind:src="require('./assets/img/' + $route.params.img)" width="200">
      </div>
      <div>
        Your Address: {{$route.params.address}}
      </div>
      <div>
        Send To: <input type="text" v-model="to_address" size="48">
      </div>
      <div>
        <img id="sendtoken" v-on:click="transfer()" v-bind:src="require('./assets/button_send.png')">
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import {Qweb3} from 'qweb3'
export default {
  data () {
    return {
      to_address: ''
    }
  },
  methods: {
    transfer () {
      let that = this
      let account
      console.log(that.to_address)
      console.log(that.$route)
      const qweb3 = new Qweb3(window.qrypto.rpcProvider)
      const contractAddress = 'f90c8e18c39209c81231ff80ad2c9f1c88a2cc0d'	//	Main Contract
      const contractAbi = [{"name":"tokenExists","type":"function","payable":false,"inputs":[{"name":"","type":"uint256","indexed":false}],"outputs":[{"name":"","type":"bool","indexed":false}],"constant":true,"anonymous":false},{"name":"name","type":"function","payable":false,"inputs":[],"outputs":[{"name":"","type":"string","indexed":false}],"constant":true,"anonymous":false},{"name":"totalSupply","type":"function","payable":false,"inputs":[],"outputs":[{"name":"","type":"uint256","indexed":false}],"constant":true,"anonymous":false},{"name":"balances","type":"function","payable":false,"inputs":[{"name":"","type":"address","indexed":false}],"outputs":[{"name":"","type":"uint256","indexed":false}],"constant":true,"anonymous":false},{"name":"ownerTokens","type":"function","payable":false,"inputs":[{"name":"","type":"address","indexed":false},{"name":"","type":"uint256","indexed":false}],"outputs":[{"name":"","type":"uint256","indexed":false}],"constant":true,"anonymous":false},{"name":"tokenOfOwnerByIndex","type":"function","payable":false,"inputs":[{"name":"_owner","type":"address","indexed":false},{"name":"_index","type":"uint256","indexed":false}],"outputs":[{"name":"tokenId","type":"uint256","indexed":false}],"constant":true,"anonymous":false},{"name":"transfer","type":"function","payable":false,"inputs":[{"name":"_to","type":"address","indexed":false},{"name":"_tokenId","type":"uint256","indexed":false},{"name":"_signature","type":"string","indexed":false}],"outputs":[],"constant":false,"anonymous":false},{"name":"ownerOf","type":"function","payable":false,"inputs":[{"name":"_tokenId","type":"uint256","indexed":false}],"outputs":[{"name":"_owner","type":"address","indexed":false}],"constant":true,"anonymous":false},{"name":"ownerTokenLength","type":"function","payable":false,"inputs":[{"name":"","type":"address","indexed":false}],"outputs":[{"name":"","type":"uint256","indexed":false}],"constant":true,"anonymous":false},{"name":"balanceOf","type":"function","payable":false,"inputs":[{"name":"_owner","type":"address","indexed":false}],"outputs":[{"name":"balance","type":"uint256","indexed":false}],"constant":true,"anonymous":false},{"name":"owner","type":"function","payable":false,"inputs":[],"outputs":[{"name":"","type":"address","indexed":false}],"constant":true,"anonymous":false},{"name":"symbol","type":"function","payable":false,"inputs":[],"outputs":[{"name":"","type":"string","indexed":false}],"constant":true,"anonymous":false},{"name":"mint","type":"function","payable":false,"inputs":[{"name":"_to","type":"address","indexed":false},{"name":"_tokenId","type":"uint256","indexed":false},{"name":"_signature","type":"string","indexed":false}],"outputs":[],"constant":false,"anonymous":false},{"name":"tokenOwners","type":"function","payable":false,"inputs":[{"name":"","type":"uint256","indexed":false}],"outputs":[{"name":"","type":"address","indexed":false}],"constant":true,"anonymous":false},{"name":"","type":"constructor","payable":false,"inputs":[],"outputs":null,"constant":false,"anonymous":false},{"name":"Transfer","type":"event","payable":false,"inputs":[{"name":"_from","type":"address","indexed":true},{"name":"_to","type":"address","indexed":true},{"name":"_tokenId","type":"uint256","indexed":false},{"name":"_signature","type":"string","indexed":false}],"outputs":null,"constant":false,"anonymous":false}]

      let contract = qweb3.Contract(contractAddress, contractAbi)

      function onQryptoAcctChange(event) {
        if (event.data.message && event.data.message.type == "ACCOUNT_CHANGED") {
          account = event.data.message.payload
        }
      }
      window.addEventListener('message', onQryptoAcctChange, false)

      const sendTransaction = async function() {
        let signature = (Math.random() * 100000000000000000).toString(16)
        const tx = await contract.send('transfer', {
          methodArgs: [that.to_address, that.$route.params.id, signature],
          gasLimit: 100000000,
          senderAddress: account.address,
          //senderAddress: 'qZnJXa8X8fMSPgkdNqPibDLRAjwt9HfYno',
        })
        .then((res) => {
          console.log(res)
        })
        .catch((res) => {
          console.error(res)
        })
      }
      sendTransaction()
    }
  }
}
</script>

<style>
#contents {
  text-align: center;
}
#sendtoken {
  margin-top: 15px;
  width: 120px;
  cursor: pointer;
}
</style>
