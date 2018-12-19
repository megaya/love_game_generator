<template>
  <div class="container">
    <h1>Crop on canvas with Cropper</h1>
    <h3>Image</h3>

    <div class="upload"><input type="file" name="file" @change="loadLocalImage"></div>

		<vue-cropper
				ref='cropper'
				:guides="true"
				:view-mode="2"
				drag-mode="crop"
				:auto-crop-area="0.5"
				:min-container-width="250"
				:min-container-height="180"
				:background="true"
				:rotatable="true"
				:src="cropperOptions.img"
				alt="Source Image"
				:img-style="{ 'width': '980px', 'height': '580px' }">
				:cropmove="test"
		</vue-cropper>
    <button @click="cropImage" style="margin-right: 40px;">Crop</button>

    <img src="./assets/message.png" style="display:none" ref="image1"></img>

    <h3>Canvas</h3>
    <div>
      <canvas id="canvas" class="img-canvas" width="980" height="580" ref="canvas"></canvas>
    </div>

    <a href="" id="download_link" ref="downloadLink">ダウンロード</a>
  </div>
</template>

<script>
import $ from 'jquery'
import VueCropper from 'vue-cropperjs'
// import Vue from 'vue/dist/vue.js'

export default {
  name: 'App',
  data: function() {
    return {
      downloadLink: "",
      cropperOptions: {
        img: null,
        size: 1,
        full: false,
        outputType: 'jpg',
        canMove: true,
        fixedBox: false,
        original: false,
        canMoveBox: true,
        fixed: true, // 比率固定を有効にする
        fixedNumber: [1, 1] // 1:1 の比率
      }
    }
  },
  methods: {
    loadLocalImage: function(e) {
      // ファイル情報を取得
      let fileData = e.target.files[0] || e.dataTransfer.files[0];
      // FileReaderオブジェクトを使ってファイル読み込み
      let reader = new FileReader();
      var self = this

			reader.onload = function(event) {
				self.cropperOptions.img = event.target.result;
				// rebuild cropperjs with the updated source
				self.$refs.cropper.replace(event.target.result);
			};
			reader.readAsDataURL(fileData);
    },
    cropImage() {
      var cropperData = this.$refs.cropper.getData();
      console.log(cropperData);

      var data = {
        x: Math.round(cropperData.x),
        y: Math.round(cropperData.y),
        width: Math.round(cropperData.width),
        height: Math.round(cropperData.height),
        vectorX: 1,
        vectorY: 1
      };

      this.$refs.canvas.getContext('2d').drawImage(
        this.$refs.cropper.$refs.img,
        data['x'],
        data['y'],
        data['width'],
        data['height'],
        0,0,//切り出されるCanvas内での座標指定
        data['vectorX'] * 980, //切り出される画像の横幅
        data['vectorY'] * 580 //切り出される画像の縦幅
      );

      this.$refs.canvas.getContext('2d').drawImage(
        this.$refs.image1,
        0,
        0,
        1280,
        204,
        0,380,//切り出されるCanvas内での座標指定
        980, //切り出される画像の横幅
        200 //切り出される画像の縦幅
      );

      this.$refs.downloadLink.href = this.$refs.canvas.toDataURL();
      this.$refs.downloadLink.download = 'sample.jpg';
    },
  },
  components: { VueCropper },
}
</script>

<style>
</style>
