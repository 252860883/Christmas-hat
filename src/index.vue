<template>
  <div id="app">
    <!-- accept属性限制上传文件类型 -->
    <input type="file" @change="fileChange" accept="image/jpeg,image/png,image/gif">
    <img :src="imgUrl">
  </div>
</template>

<script>
export default {
  name: "index",
  data() {
    return {
      imgUrl:""
    };
  },
  methods: {
    fileChange(event) {
      let self = this;
      let reader = new FileReader();
      let img = event.target.files[0];
      console.log(img);
      reader.readAsDataURL(img);
      reader.onloadend=function(){
        self.imgUrl=reader.result;
      }
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
