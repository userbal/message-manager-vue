<template>
  <div id="app">

    <div class="navbar">
      <div v-if="!messageOrConfigure" class="nav-option" @click="messageOrConfigure = !messageOrConfigure">Messages</div>
      <div v-if="messageOrConfigure" class="nav-option" @click="messageOrConfigure = !messageOrConfigure">Configure</div>
    </div>

    <div class="configure" v-if="!messageOrConfigure" >
      <ul v-for="config of configs" :key="config.id">
      <div class="config" >
        <Director :channels=discordChannels />
        <div class="remove-config" @click="removeConfig">×</div>
      </div>
      </ul>
      <div class="add-config" @click="addConfig" >+</div>
    </div>

    <div class="message-container" v-if="messageOrConfigure" >
      <ul class="messages">
        <h1>Slack</h1>
        <li class="slackMessage" v-for="item in slackMessages" :key="item.id">
          <Message :message=item.text :author=item.username :service="item.service" :channel=item.channel />
        </li>
      </ul>
      <ul class="messages">
        <h1>Discord</h1>
        <li class="discordMessages" v-for="item in discordMessages" :key="item.id">
          <Message :message=item.text :author=item.username :service="item.service" :channel=item.channel />
        </li>
      </ul>
    </div>

  </div>
</template>

<script>
import Director from './components/Director.vue'
import Message from './components/Message.vue'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    Message,
    Director
  },
  data() {
    return {
      configs: [{id: Math.random()}],
    slackChannels: [],
    slackMessages: [],
    discordChannels: [],
    discordGuilds: [],
    discordMessages: [],
    messageOrConfigure: false,
    }
  },
  methods: {
    addConfig() {
      this.configs.push({id: Math.random()})
      console.log(this.configs)
    },
    removeConfig(id) {
      delete this.configs[id]
    },
    async updateSlackChannels () {
      let response = await axios.get(`https://slack.com/api/conversations.list?token=${process.env.VUE_APP_SLACK_TOKEN}&pretty=1`)
      this.slackChannels = response.data.channels
    },
    async updateSlackMessages(){
      let allMessages = []
      for (let i in this.slackChannels){
        const conversation = this.slackChannels[i]
        const response = await axios.get(`https://slack.com/api/conversations.history?token=${process.env.VUE_APP_SLACK_TOKEN}&channel=${conversation.id}`)
        const messages = response.data.messages
        for (let i in messages){
          const message = {
            username: messages[i].username,
            text: messages[i].text,
            service: 'slack',
            channel: conversation.name,
          }
          allMessages.push(message)
        }
      }
      this.slackMessages = allMessages
    },
    async updateDiscordGuilds(){
      let response = await axios.get(`https://discordapp.com/api/users/@me/guilds`, {
      headers: {
        Authorization: process.env.VUE_APP_DISCORD_TOKEN
      }
      });
     for (let i in response.data){
       this.discordGuilds.push({id: response.data[i].id, name: response.data[i].name})
     }
    },
    async updateDiscordChannels(){
     for (let guild of this.discordGuilds){
       let response = await axios.get(`https://discordapp.com/api/guilds/${guild.id}/channels`, {
       headers: {
         Authorization: process.env.VUE_APP_DISCORD_TOKEN
       }
       });
       for (let i in response.data){
         this.discordChannels.push({id: response.data[i].id, name: response.data[i].name, guild: guild.name})
       }
     }
    },
    async updateDiscordMessages(){
     let allMessages = []
     for (let channel of this.discordChannels){
       let response = await axios.get(`https://discordapp.com/api/channels/${channel.id}/messages`, {
       headers: {
         Authorization: process.env.VUE_APP_DISCORD_TOKEN
       }
       });
       const messages = response.data
       for (let i in messages){
       const message = {
         username: messages[i].author.username, //this will need to be completed with an api call
         text: messages[i].content,
         service: 'discord',
         channel: `${channel.guild}-${channel.name}`
       }
       allMessages.push(message)
       }
     }
      this.discordMessages = allMessages
    },
  },
  async mounted () {
    await this.updateDiscordGuilds()
    await this.updateDiscordChannels()
    this.updateDiscordMessages()
    await this.updateSlackChannels()
    this.updateSlackMessages()

  }
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
  margin: 10px;
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
  padding: 8px;
  margin-bottom: 5px;
  background-color: grey;
  color: black;
  font-size: 20px;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10+ and Edge */
  user-select: none; /* Standard syntax */
}
.nav-option:hover {
  color: red;
}

.message-container{
  display: flex;
  flex-direction: row;
}
.config{
  display: flex;
}
.remove-config{
  display: flex;
  color: black;
  justify-content: center;
  font-size: 35px;
  padding-top: 32px;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10+ and Edge */
  user-select: none; /* Standard syntax */
}

.remove-config:hover {
  color: red;
}
.add-config{
  float: right;
  font-size: 50px;
  -webkit-user-select: none; /* Safari */
  -ms-user-select: none; /* IE 10+ and Edge */
  user-select: none; /* Standard syntax */
}
.add-config:hover {
  color: green;
}

h1{
  color: black;
}

ul{
  list-style-type: none;
  margin: 0;
  padding: 0;
}
</style>
