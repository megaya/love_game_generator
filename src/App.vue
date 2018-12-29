<template>
  <div class="container">

    <div class="jumbotron">
      <h1 class="display-4">恋愛ゲーム風ジェネレーター</h1>
      <hr class="my-4">
      <img src="./assets/top.jpg" alt="Responsive image" width="430" height="540"  class="img-thumbnail">
      <br />
      <br />
      <p>
        画像にシュミレーションゲーム風の会話ウィンドウと選択肢を追加できます。<br />
        写真やお気に入りの画像を理想のゲームの世界に変更してみてください。<br />
        <br />
        3ステップで簡単に画像を作ることができます。
      </p>
      <ul class="list-unstyled">
        <li>1. 画像のアップロード</li>
        <li>2. 画像のサイズ調整</li>
        <li>3. 画像の編集</li>
      </ul>

      <hr class="my-4">
      <p class="description">
        要望や不具合があれば<a href="https://twitter.com/megaya0403" target="_blank">@megaya0403</a>までよろしくお願いします。<br />
        「#恋愛ゲーム風ジェネレーター」 で感想いただけると泣いて喜びます。
      </p>
    </div>

    <div class="card">
      <div class="card-header">画像のアップロード ＆ サイズ調整</div>

      <div class="card-body">
        <label class="upload-btn">
          <span class="btn-lg btn-primary">
            画像をアップロードする
            <input type="file" name="file" @change="loadLocalImage" lang="ja" style="display:none">
          </span>
        </label>

        <div @mouseover="drawCanvas" @mouseout="drawCanvas" @click="drawCanvas">
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
              :img-style="{ 'width': '600', 'height': '400px' }">
          </vue-cropper>
        </div>

      </div><!-- body -->

      <div class="card-footer">
        <p class="card-text description">
          画像は位置調整や拡大や縮小ができます。<br />
          「画像サイズ決定」ボタンを押すと下の「画像編集」に表示されます。
        </p>
        <button class="btn-lg btn-secondary" @click="drawCanvas">画像サイズ決定</button>
      </div>
    </div><!-- div card -->


    <div class="card">
      <div class="card-header"> 画像編集</div>
      <div class="card-body text-center">
        <canvas id="canvas" class="img-canvas" width="680" height="480" ref="canvas"></canvas>
      </div>
      <div class="card-footer">

        <form>
          <fieldset class="form-group">
            <div class="row">
              <legend class="col-form-label col-sm-4 pt-0">デザイン</legend>
              <div class="col-sm-8">
                <div class="form-check">
                  <input id="type1" class="form-check-input" type="radio" value="type1" v-model="materialType" :checked="materialType == 'type1'" @change="drawCanvas">
                  <label for="type1" class="form-check-label">タイプ1</label>
                </div>
                <div class="form-check">
                  <input id="type2" class="form-check-input" type="radio" value="type2" v-model="materialType" :checked="materialType == 'type2'" @change="drawCanvas">
                  <label for="type2" class="form-check-label">タイプ2</label>
                </div>
                <div class="form-check">
                  <input id="type3" class="form-check-input" type="radio" value="type3" v-model="materialType" :checked="materialType == 'type3'" @change="drawCanvas">
                  <label for="type3" class="form-check-label">タイプ3</label>
                </div>
                <div class="form-check">
                  <input id="type4" class="form-check-input" type="radio" value="type4" v-model="materialType" :checked="materialType == 'type4'" @change="drawCanvas">
                  <label for="type4" class="form-check-label">タイプ4</label>
                </div>
              </div><!-- col-sm-10 -->
            </div><!-- form radio -->
          </fieldset>

          <div class="form-group row">
            <label class="col-sm-4 col-form-label">フォント</label>
            <div class="col-sm-8">
              <select class="form-control" v-model="fontTypeCurrent" @change="drawCanvas">
                <option v-for="fontType in fontTypes" v-bind:value="fontType" class="font-type">
                  {{ fontType }}
                </option>
              </select>
            </div>
          </div>

          <div class="form-group row">
            <label for="inputEmail3" class="col-sm-4 col-form-label">名前</label>
            <div class="col-sm-8">
              <input type="text" class="form-control" v-model="messageName" @keyup="drawCanvas"></input>
            </div>
          </div>

          <div class="form-group row">
            <label for="inputEmail3" class="col-sm-4 col-form-label">メッセージ</label>
            <div class="col-sm-8">
              <textarea class="form-control textarea-message" v-model="messageText" @keyup="drawCanvas"></textarea>
            </div>
          </div>

          <fieldset class="form-group">
            <div class="row">
              <legend class="col-form-label col-sm-4 pt-0">ボタン</legend>
              <div class="col-sm-8">
                <div class="form-check">
                  <input id="system-button-check" class="form-check-input" type="checkbox" v-model="isSysytemButton" @change="drawCanvas">
                  <label for="system-button-check" class="form-check-label">システムボタンを表示する</label>
                </div>
              </div>
            </div>
          </fieldset>


          <div>
            <fieldset class="form-group">
              <div class="row">
                <legend class="col-form-label col-sm-4 pt-0">選択肢</legend>
                <div class="col-sm-8">
                  <div class="form-check">
                    <input id="choice-check" class="form-check-input" type="checkbox" v-model="isChoices" @change="drawCanvas">
                    <label for="choice-check" class="form-check-label">選択肢を表示する</label>
                  </div>
                </div>
              </div>
            </fieldset>

            <div>
              <div class="form-group row">
                <label for="inputEmail3" class="col-sm-4 col-form-label">選択肢1</label>
                <div class="col-sm-8">
                  <input class="form-control" type="text" v-model="choiceMessage1" @keyup="drawCanvas"></input>
                </div>
              </div>
              <div class="form-group row">
                <label for="inputEmail3" class="col-sm-4 col-form-label">選択肢2</label>
                <div class="col-sm-8">
                  <input class="form-control" type="text" v-model="choiceMessage2" @keyup="drawCanvas"></input>
                </div>
              </div>
            </div>

          </div><!-- 選択肢 -->

          <div class="card-body">
            <a id="download_link" class="btn-lg btn-primary" href="#" role="button" ref="downloadLink">画像ダウンロード</a>
          </div>
        </form>


        </div><!-- div card footer -->
      </div><!-- div card -->

      <social-sharing url="https://slg-generator.megaya.net"
        title="恋愛ゲーム風ジェネレーター"
        description="シュミレーションゲームの会話風の画面がブラウザで簡単に作れます！どんな写真でもすぐにゲームの世界へ…！"
        hashtags="恋愛ゲーム風ジェネレーター"
        inline-template>

        <div>
          <button type="button" class="btn btn-social btn-tw">
            <network network="twitter">
              <font-awesome-icon :icon="['fab', 'twitter']" size="lg"></font-awesome-icon> ツイート　
            </network>
          </button>
          <button type="button" class="btn btn-social btn-fb">
            <network network="facebook">
              <font-awesome-icon :icon="['fab', 'facebook']" size="lg"></font-awesome-icon> Facebookでシェア
            </network>
          </button>
        </div>
      </social-sharing>

      <ul class="list-group list-group-flush etc-description">
        <li class="list-group-item">作った人：<a href="https://twitter.com/megaya0403" target="_blank">megaya</a></li>
        <li class="list-group-item">お借りしたゲーム素材：<a href="https://kopacurve.blog.fc2.com/" target="_blank">空想曲線</a></li>
      </ul>

    <footer class="text-center copy-light">
      © 2018 megaya All rights rserved
    </footer>



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
    <!-- menu -->
    <img src="./assets/game_image/menu.png" style="display:none" ref="menu_image"></img>
    <img src="./assets/game_image/load.png" style="display:none" ref="load_image"></img>
    <img src="./assets/game_image/skip.png" style="display:none" ref="skip_image"></img>
    <img src="./assets/game_image/save.png" style="display:none" ref="save_image"></img>

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
      isChoices: true,
      isSysytemButton: true,
      materialType: 'type1',
      choiceMessage1: '',
      choiceMessage2: '',
      currentMaterialOption: {},
      downloadLink: "",
      fontTypes: ['sans-serif', 'arial', 'serif', 'Impact', 'Century', 'monospace', 'fantasy', 'HG行書体', 'MingLiU',
                  'メイリオ', 'ＭＳ ゴシック', '游ゴシック', 'Yu Gothic', 'MS PGothic',
                  'HiraMinProN-W3', 'HG明朝E', 'ＭＳ 明朝',
                  'ヒラギノ角ゴ Pro W3', 'Hiragino Kaku Gothic Pro'
      ],
      fontTypeCurrent: "",
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
              font: '40px',
              fontSize: 40,
              fontLineHeight: 10.1618,
            }
          },
          name: {
            x: 20, y: 330,
            fontStyle: 'white',
            font: '40px',
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
            font: '20px',
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
              font: '40px',
              fontSize: 40,
              fontLineHeight: 1.1618,
            }
          },
          name: {
            x: 30, y: 335,
            fontStyle: 'white',
            font: '40px',
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
            font: '25px',
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
              font: '40px',
              fontSize: 40,
              fontLineHeight: 5.1618,
            }
          },
          name: {
            x: 30, y: 330,
            fontStyle: 'black',
            font: '40px',
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
            font: '25px',
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
              font: '40px',
              fontSize: 40,
              fontLineHeight: 6.1618,
            }
          },
          name: {
            x: 30, y: 340,
            fontStyle: 'black',
            font: '40px',
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
            font: '25px',
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
    this.fontTypeCurrent = this.fontTypes[0];
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
      this.drawSystemButton();

      this.$refs.downloadLink.href = this.$refs.canvas.toDataURL();
      this.$refs.downloadLink.download = 'sample.jpg';
    },
    addFontTypeCurrent: function(str) {
      return str + " " + this.fontTypeCurrent;
    },
    drawSystemButton: function() {
      if (this.isSysytemButton) {
        let _imgWidth = 70;
        let _imgHeight = 30;

        this.canvasContext.drawImage(
          this.$refs.menu_image,
          0, 0,
          _imgWidth, _imgHeight,
          260, 300, //切り出されるCanvas内での座標指定
          _imgWidth, _imgHeight
        );
        this.canvasContext.drawImage(
          this.$refs.save_image,
          0, 0,
          _imgWidth, _imgHeight,
          340, 300, //切り出されるCanvas内での座標指定
          _imgWidth, _imgHeight
        );
        this.canvasContext.drawImage(
          this.$refs.load_image,
          0, 0,
          _imgWidth, _imgHeight,
          420, 300, //切り出されるCanvas内での座標指定
          _imgWidth, _imgHeight
        );
        this.canvasContext.drawImage(
          this.$refs.skip_image,
          0, 0,
          _imgWidth, _imgHeight,
          500, 300, //切り出されるCanvas内での座標指定
          _imgWidth, _imgHeight
        );
      }
    },
    drawMessageText: function() {
      var textOption = this.currentlmaterialOption.message.text;
      this.canvasContext.fillStyle = textOption.fontStyle;
      this.canvasContext.font = this.addFontTypeCurrent(textOption.font);

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
      this.canvasContext.font = this.addFontTypeCurrent(option.font);

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
        this.canvasContext.font = this.addFontTypeCurrent(option.font);

        this.canvasContext.fillText(this.choiceMessage1, option.textX, option.textY1);
        this.canvasContext.fillText(this.choiceMessage2, option.textX, option.textY2);
      }
    },
  },
  components: { VueCropper },
}
</script>

<style>
html {font-size: 82.5%;}
body {font-size: 1.8rem;}
ul {font-size: 1.3rem;}

.upload-btn {
  margin-top: 20px;
  margin-bottom: 40px;
}
.btn-lg {
  font-size: 1.4em;
}

form {
  font-size: 1.1em;
}

.form-check {
  margin-bottom: 15px;
}

input[type=text] {
  font-size: 30px;
}

.textarea-message {
  font-size: 30px;
}

.description {
  font-size: 1.4rem;
}

.custom-file {
  margin-bottom: 20px;
}

.card {
  margin-bottom: 30px;
}

/* SNSボタン */
.btn-social {
  width: 250px;
  color: white;
  font-size: 1.6rem;
}
/* Facebook */
.btn-fb {
  background-color: #3B5998;
}
/* Twitter */
.btn-tw {
  background-color: #00aced;
}

.etc-description {
  margin-top: 50px;
  margin-bottom: 20px;
}

.copy-light {
  opacity:0.5;
  font-size: 1.0rem;
}
</style>
