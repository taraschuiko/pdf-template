<template>
  <div id="app">
    <form>
      <label>
        <p>Company name</p>
        <input type="text" name="name">
      </label>
      <label>
        <p>Company logo</p>
        <input type="file" name="logo">
      </label>
      <button @click="download">Download</button>
    </form>
  </div>
</template>

<script>
import { PDFDocument } from 'pdf-lib';
import inBrowserDownload from 'in-browser-download';

export default {
  name: 'App',
  components: {
  },
  methods: {
    async download(e) {
      e.preventDefault();

      const templateBytesLight = new Uint8Array(
        await fetch('/templates/master_light.pdf').then((r) => r.arrayBuffer()),
      );
      const pdfLight = await PDFDocument.load(templateBytesLight);

      inBrowserDownload((await pdfLight.save()).buffer, 'light.pdf');
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
  padding: 20px;
}

p {
  margin: 0;
}

form {
  display: flex;
  flex-direction: column;
  align-items: flex-start;

  input {
    margin-bottom: 5px;
  }
}
</style>
