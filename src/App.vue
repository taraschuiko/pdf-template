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
      <button>Download</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {
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
