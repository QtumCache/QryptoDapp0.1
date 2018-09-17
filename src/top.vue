<template>
  <div>
    <div id="title">
    <img v-bind:src="require('./assets/qrypto_pretties.png')" width="400">
    </div>
    <div id="contents" v-if="owned_tokens && owned_tokens.status == 'success'">
      <ul>
      <li v-for="(token, index) in owned_tokens.data" v-bind:key="index" class="token">
        <div class="tokenid">Token ID: {{token.token_id}}</div><br />
        <router-link :to="{name: 'detail-page', params: {id: token.token_id, address: token.qtum_address, img: token.img}}">
          <img v-bind:src="require('./assets/img/' + token.img)" width="200">
        </router-link>
      </li>
      <li id="clear"></li>
      </ul>
      <img v-on:click="getToken()" id="gettoken" v-bind:src="require('./assets/button_get-one-free.png')"><br />
      <span>{{tx}}</span>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  components: {
  },
  data () {
    return {
      args: '',
      msg: '',
      posts: [],
      source: '',
      response: '',
      owned_tokens: '',
      tx: ''
    }
  },
  methods: {
    clear () {
      this.msg = ''
    },
    listTokens () {
      axios.get('http://13.78.12.28:3001/api/tokens/qZnJXa8X8fMSPgkdNqPibDLRAjwt9HfYno')
        .then((res) => {
          this.owned_tokens = res.data
        })
        .catch((res) => {
          console.error(res)
        })
    },
    getToken () {
      axios.post('http://13.78.12.28:3001/api/mint', {
        address: 'qZnJXa8X8fMSPgkdNqPibDLRAjwt9HfYno'
      })
        .then((res) => {
          if (res.data.status == 'success') {
            let url = 'https://testnet.qtum.org/tx/' + res.data.data.txid
            this.tx = '<a href="' + url + '">' + url + '</a>'
          } else {
            this.tx = 'Failed to get tokens'
          }
        })
        .catch((res) => {
          console.error(res)
        })
    }
  },
  beforeMount () {
    this.listTokens()
  }
}
</script>

<style>
ul {
  list-style: none;
}
.tokenid {
  width: 200px;
  text-align: center;
}
.token {
  float: left;
}
#contents {
  text-align: center;
}
#clear {
  clear: both;
}
#gettoken {
  width: 200px;
  cursor: pointer;
}
</style>
