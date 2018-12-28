<template>
  <div class="container">
    <div class="row">
      <h1 class="display-">シュミレーションゲーム メーカー</h1>

      <div class="jumbotron">
        <p class="lead">
          3ステップで簡単に画像をシュミレーションゲームのような会話ウィンドウと選択肢を追加できます。<br />
          写真やお気に入りの画像を理想のゲームの世界に変更してみてください。
        </p>
        <ul class="list-unstyled">
          <li>1. 画像のアップロード</li>
          <li>2. 画像のサイズ調整</li>
          <li>3. 画像の編集</li>
        </ul>
        <img src="./assets/top.jpg" class="rounded" alt="Responsive image" width="430" height="540">

        <hr class="my-4">
        <p>
          なにか不具合があれば<a href="https://twitter.com/megaya0403" target="_blank">@megaya0403</a>までよろしくお願いします。
        </p>
      </div>

      <div class="card">
        <div class="card-header">1. 画像のアップロード</div>
        <div class="card-body">
          <div class="custom-file">
            <input type="file" name="file" @change="loadLocalImage" class="custom-file-input" lang="ja">
            <label class="custom-file-label" for="validatedCustomFile">画像をアップロードする</label>
          </div>
        </div>
      </div>

      <div class="card">
        <div class="card-header">2. 画像のサイズ調整</div>
        <div @mouseover="drawCanvas" @mouseout="drawCanvas" @click="drawCanvas" class="card-body">
          <vue-cropper
              ref='cropper'
              :guides="true"
              :view-mode="2"
              drag-mode="crop"
              :auto-crop-area="0.5"
              :background="true"
              :rotatable="true"
              :src="cropperOptions.img"
              alt="Source Image"
              :img-style="{ 'width': '680', 'height': '480px' }">
          </vue-cropper>
        </div>

        <div class="card-footer">
          <p class="card-text">
            画像は位置調整や拡大や縮小ができます。<br />
            画像のサイズを決定すると「3. 画像編集」に表示されます
          </p>
          <button class="btn btn-primary" @click="drawCanvas" style="margin-right: 40px;">画像サイズ決定</button>
        </div>
      </div><!-- div card -->


      <div class="card">
        <div class="card-header">3. 画像編集</div>
        <div class="card-body">
          <canvas id="canvas" class="img-canvas" width="680" height="480" ref="canvas"></canvas>
        </div>
        <div class="card-footer">

          <form>
            <fieldset class="form-group">
              <div class="row">
                <legend class="col-form-label col-sm-2 pt-0">デザイン</legend>
                <div class="col-sm-10">
                  <div class="form-check">
                    <input class="form-check-input" type="radio" value="type1" v-model="materialType" :checked="materialType == 'type1'" @change="drawCanvas">
                    <label class="form-check-label">タイプ1</label>
                  </div>
                  <div class="form-check">
                    <input class="form-check-input" type="radio" value="type2" v-model="materialType" :checked="materialType == 'type2'" @change="drawCanvas">
                    <label class="form-check-label">タイプ2</label>
                  </div>
                  <div class="form-check">
                    <input class="form-check-input" type="radio" value="type3" v-model="materialType" :checked="materialType == 'type3'" @change="drawCanvas">
                    <label class="form-check-label">タイプ3</label>
                  </div>
                  <div class="form-check">
                    <input class="form-check-input" type="radio" value="type4" v-model="materialType" :checked="materialType == 'type4'" @change="drawCanvas">
                    <label class="form-check-label">タイプ4</label>
                  </div>
                </div><!-- col-sm-10 -->
              </div><!-- form radio -->
            </fieldset>

            <div class="form-group row">
              <label for="inputEmail3" class="col-sm-2 col-form-label">名前</label>
              <div class="col-sm-10">
                <input type="text" class="form-control" v-model="messageName" @keyup="drawCanvas"></input>
              </div>
            </div>

            <div class="form-group row">
              <label for="inputEmail3" class="col-sm-2 col-form-label">メッセージ</label>
              <div class="col-sm-10">
                <textarea class="form-control" v-model="messageText" @keyup="drawCanvas"></textarea>
              </div>
            </div>

            <div>
              <fieldset class="form-group">
                <div class="row">
                  <legend class="col-form-label col-sm-2 pt-0">選択肢</legend>
                  <div class="col-sm-10">
                    <div class="form-check">
                      <input class="form-check-input" type="checkbox" v-model="isChoices" @change="drawCanvas">
                      <label class="form-check-label">選択肢を表示する</label>
                    </div>
                  </div>
                </div>
              </fieldset>

              <div v-show="isChoices">
                <div class="form-group row">
                  <label for="inputEmail3" class="col-sm-2 col-form-label">選択肢1</label>
                  <div class="col-sm-10">
                    <input class="form-control" type="text" v-model="choiceMessage1" @keyup="drawCanvas"></input>
                  </div>
                </div>
                <div class="form-group row">
                  <label for="inputEmail3" class="col-sm-2 col-form-label">選択肢2</label>
                  <div class="col-sm-10">
                    <input class="form-control" type="text" v-model="choiceMessage2" @keyup="drawCanvas"></input>
                  </div>
                </div>
              </div>

            </div><!-- 選択肢 -->

          </form>

        </div><!-- div card footer -->
      </div><!-- div card -->

      <div class="card">
        <div class="card-header">4. 画像のダウンロード</div>
        <div class="card-body">
          <a id="download_link" class="btn btn-primary" href="#" role="button" ref="downloadLink">画像ダウンロード</a>
        </div>
      </div>


    </div><!-- row -->

    <img src="./assets/game_image/sample.png" style="display:none" ref="sample_image"></img>
    <!-- type1 -->
    <img src="./assets/game_image/type1/message.png" style="display:none" ref="type1_image"></img>
    <img src="./assets/game_image/type1/button.png" style="display:none" ref="type1_choice1"></img>
    <img src="./assets/game_image/type1/button3.png" style="display:none" ref="type1_choice2"></img>
    <!-- type2 -->
    <img src="./assets/game_image/type2/message.png" style="display:none" ref="type2_image"></img>
    <img src="./assets/game_image/type2/button.png" style="display:none" ref="type2_choice1"></img>
    <img src="./assets/game_image/type2/button3.png" style="display:none" ref="type2_choice2"></img>
    <!-- type3 -->
    <img src="./assets/game_image/type3/message.png" style="display:none" ref="type3_image"></img>
    <img src="./assets/game_image/type3/button.png" style="display:none" ref="type3_choice1"></img>
    <img src="./assets/game_image/type3/button3.png" style="display:none" ref="type3_choice2"></img>
    <!-- type4 -->
    <img src="./assets/game_image/type4/message.png" style="display:none" ref="type4_image"></img>
    <img src="./assets/game_image/type4/button.png" style="display:none" ref="type4_choice1"></img>
    <img src="./assets/game_image/type4/button3.png" style="display:none" ref="type4_choice2"></img>

  </div>
</template>

<script>
import $ from 'jquery'
import VueCropper from 'vue-cropperjs'
import Vue from 'vue/dist/vue.js'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

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
      downloadLink: "",
      materialOptions: {
        'type1': {
          message: {
            imageWidth: 1280,
            ImageHeight: 204,
            x: 0, y: 290,
            drawWidth: 680,
            drawHeight: 200,
            'text': {
              height: 390,
              width: 30,
              fontStyle: 'black',
              font: '40px Weltron Urban',
              fontSize: 40,
              fontLineHeight: 1.1618,
            }
          },
          name: {
            x: 20, y: 330,
            fontStyle: 'white',
            font: '40px Weltron Urban',
          },
          choice: {
            imageWidth: 450,
            ImageHeight: 47,
            x: 100,
            y1: 100,
            y2: 220,
            drawWidth: 450,
            drawHeight: 47,
            fontStyle: 'black',
            font: '20px Weltron Urban',
            textX: 160,
            textY1: 133,
            textY2: 253,
          },
        },
        'type2': {
          message: {
            imageWidth: 660,
            ImageHeight: 200,
            x: 10, y: 280,
            drawWidth: 660,
            drawHeight: 200,
            'text': {
              height: 390,
              width: 60,
              fontStyle: 'black',
              font: '40px Weltron Urban',
              fontSize: 40,
              fontLineHeight: 1.1618,
            }
          },
          name: {
            x: 30, y: 335,
            fontStyle: 'white',
            font: '40px Weltron Urban',
          },
          choice: {
            imageWidth: 486,
            ImageHeight: 46,
            x: 120,
            y1: 100,
            y2: 220,
            drawWidth: 486,
            drawHeight: 46,
            fontStyle: 'black',
            font: '25px Weltron Urban',
            textX: 140,
            textY1: 130,
            textY2: 250,
          },
        },
        'type3': {
          message: {
            imageWidth: 660,
            ImageHeight: 200,
            x: 10, y: 280,
            drawWidth: 660,
            drawHeight: 200,
            'text': {
              height: 390,
              width: 40,
              fontStyle: 'black',
              font: '40px Weltron Urban',
              fontSize: 40,
              fontLineHeight: 1.1618,
            }
          },
          name: {
            x: 30, y: 330,
            fontStyle: 'black',
            font: '40px Weltron Urban',
          },
          choice: {
            imageWidth: 512,
            ImageHeight: 58,
            x: 80,
            y1: 100,
            y2: 220,
            drawWidth: 512,
            drawHeight: 58,
            fontStyle: 'black',
            font: '25px Weltron Urban',
            textX: 110,
            textY1: 140,
            textY2: 260,
          },
        },
        'type4': {
          message: {
            imageWidth: 660,
            ImageHeight: 170,
            x: 10, y: 290,
            drawWidth: 660,
            drawHeight: 170,
            'text': {
              height: 390,
              width: 90,
              fontStyle: 'black',
              font: '40px Weltron Urban',
              fontSize: 40,
              fontLineHeight: 1.1618,
            }
          },
          name: {
            x: 30, y: 340,
            fontStyle: 'black',
            font: '40px Weltron Urban',
          },
          choice: {
            imageWidth: 550,
            ImageHeight: 50,
            x: 70,
            y1: 80,
            y2: 200,
            drawWidth: 550,
            drawHeight: 50,
            fontStyle: 'black',
            font: '25px Weltron Urban',
            textX: 90,
            textY1: 115,
            textY2: 235,
          },
        },
     },
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
  created: function() {
    var self = this;
    Vue.nextTick(function () {
			self.cropperOptions.img = self.$refs.sample_image.src;
			self.$refs.cropper.replace(self.$refs.sample_image.src);
      self.drawCanvas();
    })
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
        data['vectorX'] * 680, //切り出される画像の横幅
        data['vectorY'] * 480 //切り出される画像の縦幅
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

        this.canvasContext.globalAlpha = 0.8; //透過
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

        this.canvasContext.globalAlpha = 1.0; //透過解除
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
