<template>
  <div class="director">
    <h2>Send</h2>
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
    <h2>messages to</h2>
    <div class="dropdowns">
      <h5>Service:</h5>
      <select v-model="selectedFromService">
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
    <div class="activateAndDelete">
    <button class="button" @click=activate()> {{ activateMessage }} </button>
    <button class="button" v-on:click="$emit('remove', config.id)">Delete</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Director',
  props: {
    config: null,
    discordChannels: null,
    slackChannels: null,
  },
  computed: {
    channelsTo: function() {
      if (this.selectedToService === "slack"){return this.slackChannels}
      return this.discordChannels
    },
    channelsFrom: function(){
      if (this.selectedFromService === "slack"){return this.slackChannels}
      return this.discordChannels
    }
  },
  data () {
    return {
    selectedToService: '',
    selectedToChannel: '',
    selectedFromService: '',
    selectedFromChannel: '',
    isActive: Boolean,
    activateMessage: 'Activate',
    }
  },
  methods: {
	execute () {
		this.polling = setInterval(() => {
		}, 3000)
  },
  emit () {

  },
  activate(){
    this.isActive = !this.isActive
    if (this.isActive) {
      this.execute()
      this.activateMessage = 'Activate'
    } else {
      this.activateMessage = 'Deactivate'
    }
  }
  },
  beforeDestroy () {
    clearInterval(this.polling)
  },
  created () {
    if (this. isActive){
      this.execute()
    }
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
