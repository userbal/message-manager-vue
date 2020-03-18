<template>
  <div id="app">
    <div class="navbar">
      <div v-if="!messageOrConfigure" class="nav-option" @click="messageOrConfigure = !messageOrConfigure">Messages</div>
      <div v-if="messageOrConfigure" class="nav-option" @click="messageOrConfigure = !messageOrConfigure">Configure</div>
    </div>


    <ul v-if="messageOrConfigure" class="messages">
      <li class="message" v-for="item in messages" :key="item.id">
        <Message :message=item.message :author=item.author :service="item.service" :channel=item.channel />
      </li>
    </ul>
  </div>
</template>

<script>
import Message from './components/Message.vue'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    Message
  },
  data() {
    return {
    messages: null,
    messageOrConfigure: false,
    }
  },
   mounted () {
         axios
           .get('https://api.coindesk.com/v1/bpi/currentprice.json')
           .then(response => (this.info = response))
       },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

.messages {
 width: 90%;
 margin: 0px;
 padding: 10px;
 background-color: #F9F9F9;
}
.message{
  list-style-type: none;
  margin-bottom: 30px;
}

.navbar {
  display: flex;
}

.nav-option{
  padding: 10px;
  margin-bottom: 5px;
  border-radius: 8px;
  background-color: #B64008;
  color: white;
}
.nav-option:hover {
  color: black;
}
</style>
