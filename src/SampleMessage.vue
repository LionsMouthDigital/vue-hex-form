<template>
  <div class="message" v-show="show">
    <div v-html="html"></div>
  </div>
</template>

<script>
  export default {
    name:   'SampleMessage',

    data() {
      return {
        html: '',
        show: false,
      };
    },
  };
</script>
