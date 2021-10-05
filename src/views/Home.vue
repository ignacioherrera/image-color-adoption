<template>
  <div class="home">
    <a-upload
      :file-list="fileList"
      :before-upload="previewFile"
      class="upload-input"
    >
      <a-button>
        <a-icon type="upload" /> Select File
        <a-spin v-if="fileLoading" style="margin-left: 5px" size="small" />
      </a-button>
    </a-upload>

    <color-analyser :imgSource="imageUrl"></color-analyser>
  </div>
</template>

<script>
function getBase64(img, callback) {
  const reader = new FileReader();
  reader.addEventListener("load", () => callback(reader.result));
  reader.readAsDataURL(img);
}
// @ is an alias to /src
import ColorAnalyser from "@/components/ColorAnalyser.vue";

export default {
  name: "Home",
  data() {
    return {
      imageUrl: "",
      fileList: [],
      fileLoading: false,
    };
  },
  methods: {
    previewFile(file) {
      this.fileLoading = true;
      this.fileList = [];
      getBase64(file, (imageUrl) => {
        this.imageUrl = imageUrl;
        this.fileLoading = false;
      });
      return false;
    },
  },
  components: {
    ColorAnalyser,
  },
};
</script>
<style lang="scss" scoped>
.upload-input {
  margin-top: 30px;
  margin-bottom: 30px;
}
</style>
