<template>
  <div id="app">
    <sample-message ref="responseMessage"></sample-message>

    <hex-form inline-template>
      <form
        action          = "success.html"
        @submit.prevent = "submitForm"
      >
        <label for="name">Name</label>
        <input type="text" name="name" id="name" value="Curtis Blackwell">

        <label for="favorite_band">Favorite band / artist</label>
        <input type="text" name="favorite_band" id="favorite_band" value="David Bowie">
        <button>Submit</button>
      </form>
    </hex-form>
  </div>
</template>

<script>
  import HexForm       from './components/HexForm.vue';
  import SampleMessage from './SampleMessage.vue';

  HexForm.methods.submitForm = function ($event) {
    this.showErrorMessage('<p>This demo isn\'t fully set up yet.</p>');
  };

  export default {
    name: 'app',

    components: {
      HexForm,
      SampleMessage,
    },
  };
</script>

<style>
  * + * {
    margin-top: 16px;
  }

  html {
    font-family: sans-serif;
    font-size: 16px;
  }

  body {
    margin: 0 auto;
    width: 400px;
  }

  label {
    display: inline-block;
  }

  label + * {
    margin-top: 4px;
  }

  input {
    display: block;
    font-size: 16px;
  }

  .negative {
    background-color: hsl(0, 95%, 85%);
    color: hsl(0, 95%, 15%);
    padding: 16px;
  }
</style>
