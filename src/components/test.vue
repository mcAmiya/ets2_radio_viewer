<script>
export default {
  data() {
    return {
      fileContent: null,
    };
  },
  methods: {
    onFileChange(event) {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = this.onFileRead;
        reader.readAsText(file);
      }
    },
    onFileRead(event) {
      // this.fileContent = event.target.result;
      // console.log(parseRadioData(event.target.result))
      this.fileContent = parseRadioData(event.target.result)
    },
  },
};
</script>

<template>
  <div>
    <input type="file" @change="onFileChange"/>
    <div v-if="fileContent">
      <h2>文件内容：</h2>
      <pre>{{ fileContent }}</pre>
    </div>
  </div>

</template>

<style scoped>

</style>