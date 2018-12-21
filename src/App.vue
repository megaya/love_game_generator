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
    <button @click="drawCanvas" style="margin-right: 40px;">Crop</button>

    <!-- type1 -->
    <img src="./assets/game_image/type1/message.png" style="display:none" ref="type1_image"></img>
    <img src="./assets/game_image/type1/button.png" style="display:none" ref="type1_choice1"></img>
    <img src="./assets/game_image/type1/button3.png" style="display:none" ref="type1_choice2"></img>
    <!-- type2 -->
    <img src="./assets/game_image/type2/message.png" style="display:none" ref="type2_image"></img>
    <img src="./assets/game_image/type2/button.png" style="display:none" ref="type2_choice1"></img>
    <img src="./assets/game_image/type2/button3.png" style="display:none" ref="type2_choice2"></img>

    <h3>Canvas</h3>
    <div>
      <canvas id="canvas" class="img-canvas" width="980" height="580" ref="canvas"></canvas>
    </div>

    <div>
      <div>
        デザインタイプ
        <br>
        <input type="radio" value="type1" v-model="materialType" :checked="materialType == 'type1'" @change="drawCanvas">
        <label for="one">1</label>
        <br>
        <input type="radio" value="type2" v-model="materialType" :checked="materialType == 'type2'" @change="drawCanvas">
        <label for="two">2</label>
        <br>
      </div>
      <div>
        名前<input type="text" v-model="messageName" @keyup="drawCanvas"></input>
      </div>
      <div>
        メッセージ<textarea v-model="messageText" @keyup="drawCanvas"></textarea>
      </div>
      <div>
        選択肢
        <input type="checkbox" v-model="isChoices" @change="drawCanvas">
        選択肢1<input type="text" v-model="choiceMessage1" @keyup="drawCanvas"></input>
        選択肢2<input type="text" v-model="choiceMessage2" @keyup="drawCanvas"></input>
      </div>
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
      canvasContext: {},
      messageName: "",
      messageText: "",
      isChoices: false,
      materialType: 'type1',
      choiceMessage1: '',
      choiceMessage2: '',
      currentMaterialOption: {},
      materialOptions: {
        'type1': {
          message: {
            imageWidth: 1280,
            ImageHeight: 204,
            x: 0, y: 380,
            drawWidth: 980,
            drawHeight: 200,
            'text': {
              height: 470,
              width: 30,
              fontStyle: 'black',
              font: '40px Weltron Urban',
              fontSize: 40,
              fontLineHeight: 1.1618,
            }
          },
          name: {
            x: 30, y: 420,
            fontStyle: 'white',
            font: '40px Weltron Urban',
          },
          choice: {
            imageWidth: 660,
            ImageHeight: 70,
            x: 150,
            y1: 120,
            y2: 240,
            drawWidth: 660,
            drawHeight: 70,
            fontStyle: 'black',
            font: '25px Weltron Urban',
            textX: 240,
            textY1: 165,
            textY2: 285,
          },
        },
        'type2': {
          message: {
            imageWidth: 960,
            ImageHeight: 200,
            x: 10, y: 370,
            drawWidth: 960,
            drawHeight: 200,
            'text': {
              height: 480,
              width: 60,
              fontStyle: 'black',
              font: '40px Weltron Urban',
              fontSize: 40,
              fontLineHeight: 1.1618,
            }
          },
          name: {
            x: 40, y: 425,
            fontStyle: 'white',
            font: '40px Weltron Urban',
          },
          choice: {
            imageWidth: 486,
            ImageHeight: 46,
            x: 240,
            y1: 120,
            y2: 240,
            drawWidth: 486,
            drawHeight: 46,
            fontStyle: 'black',
            font: '25px Weltron Urban',
            textX: 260,
            textY1: 155,
            textY2: 275,
          },
       },
     },
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
    drawCanvas() {
      this.canvasContext = this.$refs.canvas.getContext('2d');
      this.canvasContext.clearRect(0,0, this.canvasContext.canvas.width, this.canvasContext.canvas.height);
      this.currentlmaterialOption = this.materialOptions[this.materialType]

      var cropperData = this.$refs.cropper.getData();
      var data = {
        x: Math.round(cropperData.x),
        y: Math.round(cropperData.y),
        width: Math.round(cropperData.width),
        height: Math.round(cropperData.height),
        vectorX: 1,
        vectorY: 1
      };

      this.canvasContext.drawImage(
        this.$refs.cropper.$refs.img,
        data['x'],
        data['y'],
        data['width'],
        data['height'],
        0,0,//切り出されるCanvas内での座標指定
        data['vectorX'] * 980, //切り出される画像の横幅
        data['vectorY'] * 580 //切り出される画像の縦幅
      );

      var messageOption = this.currentlmaterialOption.message
      // メッセージウィンドウ
      this.canvasContext.drawImage(
        this.$refs[this.materialType + '_image'],
        0,
        0,
        messageOption.imageWidth, //画像の横幅
        messageOption.ImageHeight, // 画像の縦幅
        messageOption.x, //切り出されるCanvas内での座標指定
        messageOption.y,
        messageOption.drawWidth, //切り出される画像の横幅
        messageOption.drawHeight //切り出される画像の縦幅
      );

      this.drawMessageName();
      this.drawMessageText();
      this.drawChoices();

      this.$refs.downloadLink.href = this.$refs.canvas.toDataURL();
      this.$refs.downloadLink.download = 'sample.jpg';
    },
    drawMessageText: function() {
      var textOption = this.currentlmaterialOption.message.text;
      this.canvasContext.fillStyle = textOption.fontStyle;
      this.canvasContext.font = textOption.font;

			for(var lines=this.messageText.split("\n"), i=0, l=lines.length; l>i; i++) {
        var lineTextHeight = textOption.height;
        if (i != 0) { lineTextHeight = textOption.height + (textOption.fontSize + textOption.fontLineHeight) * i; }

				// 2行目以降の水平位置は行数とlineHeightを考慮する
        this.canvasContext.fillText(lines[i], textOption.width, lineTextHeight);
			}
    },
    drawMessageName: function() {
      var option = this.currentlmaterialOption.name
      this.canvasContext.fillStyle = option.fontStyle;
      this.canvasContext.font = option.font;
      this.canvasContext.fillText(this.messageName, option.x, option.y);
    },
    drawChoices: function() {
      if (this.isChoices) {
        var option = this.currentlmaterialOption.choice;

        this.canvasContext.drawImage(
          this.$refs[this.materialType + "_choice2"],
          0,
          0,
          option.imageWidth, //画像の横幅
          option.ImageHeight, // 画像の縦幅
          option.x,
          option.y1,//切り出されるCanvas内での座標指定
          option.drawWidth, //切り出される画像の横幅
          option.drawHeight//切り出される画像の縦幅
        );
        this.canvasContext.drawImage(
          this.$refs[this.materialType + "_choice1"],
          0,
          0,
          option.imageWidth, //画像の横幅
          option.ImageHeight, // 画像の縦幅
          option.x,
          option.y2,//切り出されるCanvas内での座標指定
          option.drawWidth, //切り出される画像の横幅
          option.drawHeight//切り出される画像の縦幅
        );

        this.canvasContext.fillStyle = option.fontStyle;
        this.canvasContext.font = option.font;
        this.canvasContext.fillText(this.choiceMessage1, option.textX, option.textY1);
        this.canvasContext.fillText(this.choiceMessage2, option.textX, option.textY2);
      }
    },
  },
  components: { VueCropper },
}
</script>

<style>
</style>
