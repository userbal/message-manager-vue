<template>
  <div class="navbar">
    <div class="nav-option">Messages</div>
    <div class="nav-option">Configure</div>
  </div>
</template>

<script>
export default {
  name: 'Message',
  props: {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.message{
  border-bottom: solid;
}
.message-info{
  font-weight: 100;
}
</style>
