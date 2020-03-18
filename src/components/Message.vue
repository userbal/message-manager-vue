<template>
  <div class="message">
    <div class="message-info">
      <div class="message-info"> <b>{{ author }}</b> from <b>{{ service }}-{{ channel }}</b> </div>
    </div>
    <div class="message-body"> {{ message }} </div>
  </div>
</template>

<script>
export default {
  name: 'Message',
  props: {
    message: String,
    author: String,
    service: String,
    channel: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.message{
}
.message-info{
  font-size: 19px;
  padding-bottom: 5px;
}
</style>
