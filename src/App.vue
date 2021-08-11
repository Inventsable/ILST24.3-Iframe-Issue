<template>
  <div id="app">
    <button id="button" label="test">Click to test</button>
    <textarea id="text" placeholder="No text" />
  </div>
</template>

<script>
export default {
  data: () => ({
    result: "",
  }),
  mounted() {
    console.log("IS DEV:", this.isDev);
    document.querySelector("#button").onclick = (evt) => {
      this.testClick();
    };
  },
  computed: {
    isDev() {
      return /localhost/i.test(window.location);
    },
  },
  methods: {
    async testClick() {
      console.log("Click");
      let testCode = `(function() {
        var testing = "Hello world";
        alert(testing);
        return testing;
      }())`;
      let result = await this.evalScript(testCode);
      console.log("RESULT:");
      console.log(result);
      document.querySelector("#text").innerHTML = result;
    },
    evalScript(text) {
      return new Promise((resolve, reject) => {
        const CS_Interface = new CSInterface();
        CS_Interface.evalScript(`${text}`, (res) => {
          console.log("INNER CALLBACK:", res); // Never fired when iframe is present, even though script is invoked and alert appears
          resolve(res);
        });
      });
    },
  },
};
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
}

button {
  padding: 10px 5px;
  margin-bottom: 10px;
}
</style>