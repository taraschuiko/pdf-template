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
import { PDFDocument, rgb } from 'pdf-lib';
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

      this.processPdf('/templates/master_light.pdf', 'light.pdf');
      this.processPdf('/templates/master_dark.pdf', 'light.pdf');
    },
    async processPdf(path, name) {
      const templateBytesLight = new Uint8Array(
        await fetch(path).then((r) => r.arrayBuffer()),
      );

      const pdf = await PDFDocument.load(templateBytesLight);

      const page = pdf.getPage(0);

      let logo = null;

      try {
        logo = await pdf.embedPng(this.logo);
      } catch {
        logo = await pdf.embedJpg(this.logo);
      }

      if (logo) {
        const logoDims = logo.scale(170 / logo.height);

        page.drawImage(logo, {
          x: page.getWidth() / 2 - logoDims.width / 2,
          y: page.getHeight() - logoDims.height - 512,
          width: logoDims.width,
          height: logoDims.height,
          opacity: 0.2,
        });
      }

      page.drawText(this.name, {
        x: 0,
        y: page.getHeight() - 615,
        size: 50,
        color: rgb(0.33, 0.38, 0.74),
      });

      inBrowserDownload((await pdf.save()).buffer, name);
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
