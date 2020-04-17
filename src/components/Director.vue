<template>
  <div class="director">
    <h2>Send</h2>
    <div class="dropdowns">
      <h5>Service:</h5>
      <select v-model="selectedToService" >
          <option v-bind:value="'slack'" v-bind:key="0"> Slack </option>
          <option v-bind:value="'discord'" v-bind:key="1"> Discord </option>
      </select>
      <h5>Channel:</h5>
      <select v-model="selectedToChannel">
          <option v-for="channel in channels" v-bind:value="channel.id" v-bind:key="channel.id">
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
          <option v-for="channel in channels" v-bind:value="channel.id" v-bind:key="channel.id">
              {{ channel.name }}
          </option>
      </select>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Director',
  props: {
    channels: null,
  },
  data () {
    return {
    selectedToService: '',
    selectedToChannel: '',
    selectedFromService: '',
    selectedFromChannel: '',
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

  .director{
    display: flex;
    margin: 10px;
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
    width: 100%;
  }
</style>
