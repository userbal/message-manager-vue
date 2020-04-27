<template>
  <div class="director">
    <h2>Send</h2>
    <div class="dropdowns">
      <h5>Service:</h5>
      <select v-model="selectedFromService" >
          <option v-bind:value="'slack'" v-bind:key="0"> Slack </option>
          <option v-bind:value="'discord'" v-bind:key="1"> Discord </option>
      </select>
      <h5>Channel:</h5>
      <select v-model="selectedFromChannel">
          <option v-for="channel in channelsFrom" v-bind:value="channel.id" v-bind:key="channel.id">
              {{ channel.name }}
          </option>
      </select>
    </div>
    <h2>messages to</h2>
    <div class="dropdowns">
      <h5>Service:</h5>
      <select v-model="selectedToService">
          <option v-bind:value="'slack'" v-bind:key="0"> Slack </option>
          <option v-bind:value="'discord'" v-bind:key="1"> Discord </option>
      </select>
      <h5>Channel:</h5>
      <select v-model="selectedToChannel">
          <option v-for="channel in channelsTo" v-bind:value="channel.id" v-bind:key="channel.id">
              {{ channel.name }}
          </option>
      </select>
    </div>
    <div class="activateAndDelete">
    <button class="button" @click=activate()> {{ activateMessage }} </button>
    <button class="button" v-on:click="$emit('remove', config.id)">Delete</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
// import discord from 'discord.js'

export default {
  name: 'Director',
  props: {
    config: null,
    discordChannels: null,
    slackChannels: null,
    active: Boolean,
  },
  computed: {
    channelsTo: function() {
      if (this.selectedToService === "slack"){return this.slackChannels}
      return this.discordChannels
    },
    channelsFrom: function(){
      if (this.selectedFromService === "slack"){return this.slackChannels}
      return this.discordChannels
    },
  },
  data () {
    return {
    selectedToService: '',
    selectedToChannel: '',
    selectedFromService: '',
    selectedFromChannel: '',
    isActive: this.active,
    messages: [],
    activateMessage: this.activateStartingValue(),
    latestTimeStamp: 0,
    // client: this.createDiscordClient,
    }
  },
  methods: {
  // createDiscordClient(){
    // const client = new discord.Client()
    // client.login(process.env.VUE_APP_DISCORD_TOKEN)
    // return client
  // },
	execute () {
		this.polling = setInterval(async () => {
      console.log("execute")
      if (this.isActive && this.selectedFromChannel != "" && this.selectedToChannel != "" && this.selectedFromService != "" && this.selectedToService != "") {
        if (this.selectedFromService == "slack"){
          await this.getSlackMessages()
          if (this.selectedToService == "discord"){
            this.postDiscordMessages()
          } else {
            this.postSlackMessages()
          }
        }
        else {
          await this.getDiscordMessages()
          if (this.selectedToService == "slack"){
            this.postSlackMessages()
          } else {
            this.postDiscordMessages()
          }
        }
      }
    }, 6000)
  },
  async getSlackMessages(){
    console.log("get slack messages")
    let allMessages = []
    const response = await axios.get(`https://slack.com/api/conversations.history?token=${process.env.VUE_APP_SLACK_TOKEN}&channel=${this.selectedFromChannel}`)
    const messages = response.data.messages
    for (let i in messages){
      const message = {
        username: messages[i].username,
        text: messages[i].text,
        service: 'slack',
        channel: this.selectedFromChannel.name,
        time: messages[i].ts
      }
      if (this.latestTimeStamp === 0){
         this.latestTimeStamp = message.time
         return
      }
      if (message.time > this.latestTimeStamp){
        this.latestTimeStamp = message.time
        allMessages.push(message)

      }
    }
      this.messages = allMessages
  },
  async getDiscordMessages(){
    console.log("discord messages")
     let allMessages = []
       let response = await axios.get(`https://discordapp.com/api/channels/${this.selectedFromChannel}/messages`, {
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
         channel: `${this.selectedFromChannel.guild}-${this.selectedFromChannel.name}`,
         time: Date.parse(messages[i].timestamp)/1000

       }
       if (this.latestTimeStamp === 0){
         this.latestTimeStamp = message.time
         return
       }
       if (message.time > this.latestTimeStamp){
        allMessages.push(message)
        this.latestTimeStamp = message.time
       }
      }
      this.messages = allMessages
  },
  async postDiscordMessages(){
    // for (let message of this.messages){
      // console.log(this.client.channels)
      // channel.send(message)
      // console.log(message)
    // }
    // for (let message of this.messages){
    //   console.log(`https://discordapp.com/api/channels/${this.selectedToChannel}/messages?content=${message.text}`)
    //   axios.post(`https://discordapp.com/api/channels/${this.selectedToChannel}/messages?content=${message.text}`, {
    //   headers: {
    //       Authorization: process.env.VUE_APP_DISCORD_TOKEN
    //    }
    //    });
    // }
      },
  postSlackMessages(){
  for (let message of this.messages){
    console.log(message.text)
      axios.post(`https://slack.com/api/chat.postMessage?token=${process.env.VUE_APP_SLACK_TOKEN}&channel=${this.selectedToChannel}&text=${message.text}`)
    }
  },
  emit () {
    console.log("emit")
    //this.$emit('update', this.selectedToService, this.selectedToChannel, this.selectedFromService, this.selectedFromChannel, this.isActive)
  },
  toService() {
    this.$emit('update', )
  },
  activate(){
    this.isActive = !this.isActive
    if (!this.isActive) {
      this.activateMessage = 'Activate'
    } else {
      this.activateMessage = 'Deactivate'
    }
  },
  activateStartingValue(){
    //this is stupid and I hate it, this whole activate process needs to be simplified
    if (this.active){
      return "Deactivate"
    }
    return "Activate"
  }
  },
  beforeDestroy () {
    clearInterval(this.polling)
  },
  created () {
    this.execute()
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .director{
    display: flex;
    margin: 5px;
  }
  .dropdowns{
    margin-left: 10px;
    margin-right: 10px;
  }
  h2{
    margin: 0px;
    padding: 0px;
  }
  h5{
    margin: 0px;
    padding: 0px;
  }

  select {
    width: 300px;
  }
  .activateAndDelete{
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    margin-top: 16px;
  }

  .active {
    color: yellowgreen;
    font-weight: 500;
  }

  .button{
    font-weight: 500;
    width: 120px;
    height: 32px;
    text-align: center;
  }
</style>
