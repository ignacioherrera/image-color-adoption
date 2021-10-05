<template>
  <div class="component-container">
    <a-button
      type="primary"
      class="analyse-button"
      @click="analyseImage"
      :loading="loading"
      :disabled="imgSource === ''"
      >Analyse</a-button
    >
    <img
      ref="resetImage"
      class="reset-image"
      :src="require('@/assets/reset.png')"
    />
    <div class="img-container">
      <img
        v-if="imgSource !== ''"
        ref="imgRef"
        class="img-used"
        :src="imgSource"
      />
      <canvas ref="canvasForAnalysis" class="canvas-analysis"></canvas>
    </div>
    <h2 class="header-response" v-if="colorList.length > 0">
      Most relevant colors
    </h2>
    <div class="flex-container" v-if="colorList.length > 0">
      <div
        class="color-div"
        v-for="color in colorList"
        :key="color[0] + color[1]"
      >
        <div
          :style="{
            backgroundColor: color[0],
            height: '20px',
            width: '100%',
            marginBottom: '5px',
          }"
        ></div>
        <p class="color-string">{{ color[0] }}</p>
        <p class="color-string">{{ ((color[1] / total) * 100).toFixed(2) }}%</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ColorAnalyser",
  props: {
    imgSource: String,
  },
  data() {
    return {
      loading: false,
      colors: {},
      colorList: [],
      total: 0,
    };
  },
  computed: {},
  methods: {
    analyseImage() {
      this.colors = {};
      this.colorList = [];
      this.loading = true;
      this.$refs.canvasForAnalysis.width;
      this.$refs.canvasForAnalysis.width = this.$refs.imgRef.width;
      this.$refs.canvasForAnalysis.height = this.$refs.imgRef.height;
      this.$refs.canvasForAnalysis
        .getContext("2d")
        .drawImage(
          this.$refs.imgRef,
          0,
          0,
          this.$refs.imgRef.width,
          this.$refs.imgRef.height
        );
      let imgData = this.$refs.canvasForAnalysis
        .getContext("2d")
        .getImageData(0, 0, this.$refs.imgRef.width, this.$refs.imgRef.height);
      let pixelsData = imgData.data;
      let count = 0;
      for (let i = 0; i < pixelsData.length; i += 4) {
        let red = pixelsData[i];
        let green = pixelsData[i + 1];
        let blue = pixelsData[i + 2];
        let alpha = pixelsData[i + 3];
        if (alpha !== 0) {
          count++;
          let color = this.rgbToHex(red, green, blue);
          if (this.colors[color] === undefined) this.colors[color] = 1;
          else this.colors[color]++;
        }
      }
      this.total = count;
      this.processColorList();
      this.$refs.canvasForAnalysis
        .getContext("2d")
        .clearRect(
          0,
          0,
          this.$refs.canvasForAnalysis.width,
          this.$refs.canvasForAnalysis.height
        );
      this.loading = false;
    },
    componentToHex(c) {
      var hex = c.toString(16);
      return hex.length == 1 ? "0" + hex : hex;
    },
    processColorList() {
      let list = Object.entries(this.colors);
      list.sort(function (a, b) {
        return b[1] - a[1];
      });

      this.colorList = list.slice(0, 9);
    },

    rgbToHex(r, g, b) {
      return (
        "#" +
        this.componentToHex(r) +
        this.componentToHex(g) +
        this.componentToHex(b)
      );
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.img-used {
  width: 100%;
  height: auto;
  max-width: 500px;
}
.header-response {
  text-align: center;
  margin-top: 20px;
}
.img-container {
  text-align: center;
}
.analyse-button {
  margin-top: 20px;
  margin-bottom: 20px;
}
.color-div {
  width: 9.5%;
  padding: 5px;
}
.color-string {
  margin-top: 5px;
  margin-bottom: 5px;
}
.reset-image {
  display: none;
}
.flex-container {
  display: flex;
  overflow: hidden;
  justify-content: center;
  .child-container {
    width: 50%;
  }
}
.canvas-analysis {
  display: none;
}
</style>
