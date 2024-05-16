<template>
  <v-container>
    <v-row>
      <v-col cols="12" md="6" offset-md="3">
        <v-text-field
          v-model="imageUrl"
          label="Image URL"
          outlined
          dense
          solo
          clearable
        />
        <v-btn @click="loadImageFromUrl" color="primary">Load Image</v-btn>
        <input type="file" @change="onFileChange" accept="image/*" />
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12" md="6" offset-md="3">
        <img
          v-if="imageUrl"
          :src="imageUrl"
          @load="onImageLoad"
          ref="image"
          style="display: none;"
        />
        <canvas
          v-show="loadedImage"
          ref="canvas"
          @mousemove="getColorInfo"
          style="border: 1px solid black; display: block;"
        ></canvas>
        <v-row v-show="loadedImage">
          <v-col cols="12">
            <v-card>
              <v-card-text>
                <p>Image Size: {{ imageWidth }} x {{ imageHeight }}</p>
                <p v-if="colorInfo">Color Info: {{ colorInfo }}</p>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      imageUrl: '',
      loadedImage: false,
      imageWidth: 0,
      imageHeight: 0,
      colorInfo: ''
    };
  },
  methods: {
    loadImageFromUrl() {
      this.loadedImage = false;
    },
    onFileChange(e) {
      const file = e.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = (event) => {
          this.imageUrl = event.target.result;
          this.loadedImage = false;
        };
        reader.readAsDataURL(file);
      }
    },
    onImageLoad() {
      const image = this.$refs.image;
      this.imageWidth = image.naturalWidth;
      this.imageHeight = image.naturalHeight;
      const canvas = this.$refs.canvas;
      const ctx = canvas.getContext('2d');
      canvas.width = image.naturalWidth;
      canvas.height = image.naturalHeight;
      ctx.drawImage(image, 0, 0);
      this.loadedImage = true;
    },
    getColorInfo(event) {
      const canvas = this.$refs.canvas;
      const rect = canvas.getBoundingClientRect();
      const x = event.clientX - rect.left;
      const y = event.clientY - rect.top;
      const ctx = canvas.getContext('2d');
      const pixelData = ctx.getImageData(x, y, 1, 1).data;
      const color = `RGB(${pixelData[0]}, ${pixelData[1]}, ${pixelData[2]})`;
      this.colorInfo = `Color: ${color}, Position: (${x}, ${y})`;
    }
  }
};
</script>

<style scoped>
canvas {
  width: 100%;
  max-width: 100%;
}
</style>



