<template>
  <div id="app">
    <form>
      <label>
        <p>Company name</p>
        <input type="text" name="name" v-model="name">
      </label>
      <label>
        <p>Company logo</p>
        <input type="file" name="logo" @change="logoUpload($event)">
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
  data: () => ({
    name: '',
    logo: null,
  }),
  components: {
  },
  methods: {
    async download(event) {
      event.preventDefault();

      const templateBytesLight = new Uint8Array(
        await fetch('/templates/master_light.pdf').then((r) => r.arrayBuffer()),
      );
      const pdfLight = await PDFDocument.load(templateBytesLight);

      const page = pdfLight.getPage(0);

      let logo;

      try {
        logo = await pdfLight.embedPng(this.logo);
      } catch {
        logo = await pdfLight.embedJpg(this.logo);
      }

      const logoDims = logo.scale(170 / logo.height);

      page.drawImage(logo, {
        x: page.getWidth() / 2 - logoDims.width / 2,
        y: page.getHeight() - logoDims.height - 512,
        width: logoDims.width,
        height: logoDims.height,
      });

      inBrowserDownload((await pdfLight.save()).buffer, 'light.pdf');
    },
    async logoUpload(event) {
      this.logo = new Uint8Array(await event.target.files[0].arrayBuffer());
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
