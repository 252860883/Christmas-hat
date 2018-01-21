<template>
  <div id="app">

    <div class="title">
      <!-- <p>MERRY CHRISMAS</p> -->
      <img src="./assets/bg.png" alt="">
    </div>

    <!-- 照片区 -->
    <div class="img-top">
      
      <div class="img-border">
        <div class="made-img">
          <!-- 大图 -->
          <img class="img" :src="imgUrl" @click="showBorder=false"/>   
          <!-- 素材区 -->
          <div class="hat-con" v-show="showHat" @touchstart="showBorder=true">
            <div class="hat-border" v-show="showBorder">
              <div class="del" @touchmove="moveBar($event)">
                <p class="bar">||</p>
              </div>
            </div>
            <img class="imghat"  :src="hatUrl" @touchstart="moveStart($event)" @touchmove="moveHat($event)" @touchend="moveEnd()"/>          
          </div>
        </div>
      </div>

      <div class="button-group">
        <!-- accept属性限制上传文件类型 -->
        <input class="upload" type="file" @change="fileChange" accept="image/jpeg,image/png,image/jpg">
        <a @click="drawCanvas">保存</a>
      </div>
    </div>

    <!-- 选择帽子 -->
    <div class="hat-box">
      <div class="hat-select">
        <div class="hat-border" v-for="hat in hatLists" @click="selectHat(hat)"> 
          <img :src="hat">
        </div>
      </div>
    </div>

    <!-- 生成图片 -->
    <div class="download" v-show="isProduce" @click="isProduce=false">
      <img :src="downLoadImgUrl" alt="">
      <p>长按图片保存</p>
    </div>

  </div>
</template>

<script>
import html2canvas from "html2canvas";
export default {
  name: "index",
  data() {
    return {
      imgUrl: "",
      hatLists: [
        require("./assets/hat1.png"),
        require("./assets/hat2.png"),
        require("./assets/hat3.png"),
        require("./assets/hat4.png"),
        require("./assets/hat5.png")
      ],
      sPosition: {
        x: 0,
        y: 0
      },
      dX: 0,
      dY: 0,
      moveX: 0,
      moveY: 0,
      scale: 1, //放大的尺寸
      showHat: false,
      showBorder: false,
      hatUrl: "",
      downLoadImgUrl: "",
      isProduce: false,
      centerX: 0,
      centerY: 0,
      rotate: 0,
      isPicture: false
    };
  },
  methods: {
    // 上传图片显示
    fileChange(event) {
      event.preventDefault();
      this.isPicture = false;
      let self = this;
      let reader = new FileReader();
      let img = event.target.files[0];
      reader.readAsDataURL(img);
      reader.onloadend = function() {
        self.imgUrl = reader.result;
        self.isPicture = true;
      };
    },
    // 保存图片生成canvas
    drawCanvas() {
      let self = this;
      if (!self.isPicture) {
        return;
      }
      this.showBorder = false;
      setTimeout(function() {
        html2canvas(document.querySelector(".made-img")).then(canvas => {
          self.downLoadImgUrl = canvas.toDataURL("image/jpeg");
          self.isProduce = true;
        });
      }, 0);
    },
    moveStart(e) {
      e.preventDefault();
      this.sPosition = {
        x: e.touches[0].clientX,
        y: e.touches[0].clientY
      };
    },
    moveHat(e) {
      e.preventDefault();
      let moveDom = document.querySelector(".hat-con");
      this.moveX = e.touches[0].clientX - this.sPosition.x;
      this.moveY = e.touches[0].clientY - this.sPosition.y;

      moveDom.style.transform = `translateX(${this.dX +
        this.moveX}px) translateY(${this.dY + this.moveY}px) scale(${
        this.scale
      }) rotate(${this.rotate}deg)`;
      moveDom.style.webkitTransform = `translateX(${this.dX +
        this.moveX}px) translateY(${this.dY + this.moveY}px) scale(${
        this.scale
      }) rotate(${this.rotate}deg)`;
    },
    moveEnd() {
      this.dX += this.moveX;
      this.dY += this.moveY;
      this.centerX += this.moveX;
      this.centerY += this.moveY;
    },
    moveBar(e) {
      let moveDom = document.querySelector(".hat-con");
      let delDom = document.querySelector(".del");

      let rX = e.touches[0].clientX - this.centerX;
      let rY = e.touches[0].clientY - this.centerY;
      if (rX >= 0) {
        this.rotate = (Math.atan(rY / rX) - Math.PI * 0.25) / Math.PI * 180;
      } else {
        this.rotate = (Math.atan(rY / rX) + Math.PI * 0.75) / Math.PI * 180;
      }

      this.scale =
        Math.sqrt(
          Math.pow(e.touches[0].clientX - this.centerX, 2) +
            Math.pow(e.touches[0].clientY - this.centerY, 2)
        ) /
        (Math.sqrt(2) * 50);

      if (this.scale < 0.5) {
        this.scale = 0.5;
      }
      if (this.scale > 2) {
        this.scale = 2;
      }

      moveDom.style.transform = `translateX(${this.dX}px) translateY(${
        this.dY
      }px) scale(${this.scale}) rotate(${this.rotate}deg)`;
      moveDom.style.webkitTransform = `translateX(${this.dX}px) translateY(${
        this.dY
      }px) scale(${this.scale}) rotate(${this.rotate}deg)`;
      // moveDom.style.transform = `rotate(${this.rotate}deg)`;
    },
    // 还原样式
    clearHat() {
      let moveDom = document.querySelector(".hat-con");
      moveDom.style.webkitTransform = "";
      moveDom.style.transform = "";
      this.dX = 0;
      this.dY = 0;
      this.moveX = 0;
      this.moveY = 0;
      this.scale = 1;
      this.rotate = 0;
    },
    selectHat(hat) {
      if (!this.isPicture) {
        return;
      }
      this.showHat = true;
      this.showBorder = true;
      this.hatUrl = hat;
      this.clearHat();
      let self = this;
      let moveDom = document.querySelector(".hat-con");
      let delDom = document.querySelector(".del");
      setTimeout(function() {
        self.centerY =
          (delDom.getBoundingClientRect().top +
            moveDom.getBoundingClientRect().top +
            10) /
          2;
        self.centerX =
          (delDom.getBoundingClientRect().left +
            moveDom.getBoundingClientRect().left +
            10) /
          2;
        // alert(delDom.getBoundingClientRect().x);
      }, 0);
    }
  }
};
</script>

<style lang="scss">
$red: #cd0000;
body,
html {
  margin: 0;
  padding: 0;
  height: 100%;
  overflow: hidden;
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 0;
  margin-top: 30px;
  padding: 0;
  height: 100%;
  .title {
    img {
      position: fixed;
      margin-top: -30px;
      z-index: -1;
      width: 100%;
      // height: 100%;
    }
  }
  .img-top {
    width: 300px;
    margin: 0 auto;
    .img-border {
      display: block;
      width: 300px;
      height: 300px;
      border: 3px solid #ddd;
      background: #fff;
      border-radius: 5px;
      box-sizing: border-box;
      overflow: hidden;
      position: relative;
      .img {
        max-width: 300px;
        min-height: 300px;
        overflow: hidden;
        clip:rect(0,0,300px,300px);
      }
      .hat-con {
        position: absolute;
        z-index: 10;
        top: 50%;
        left: 50%;
        width: 100px;
        height: 100px;
        margin: -50px 0 0 -50px;
        .hat-border {
          position: absolute;
          border: 1px dashed #000;
          width: 100px;
          height: 100px;
          .del {
            position: absolute;
            z-index: 5;
            bottom: -25px;
            right: -25px;
            width: 50px;
            height: 50px;
            // background: #ddd;
            transform: scale(1) !important;
            .bar {
              background: #000;
              border-radius: 50%;
              width: 20px;
              height: 20px;
              line-height: 20px;
              margin: 0 auto;
              margin-top: 15px;
              color: #fff;
              text-align: center;
              font-size: 12px;
            }
          }
        }
        .imghat {
          position: absolute;
          width: 100px;
          height: 100px;
        }
      }

      canvas {
        width: 300px;
        height: 300px;
      }
    }
    .button-group {
      margin-top: 20px;
      .upload {
        position: relative;
        width: 180px;
        margin-right: 15px;
        &::after {
          content: "上传图片";
          position: absolute;
          top: 0;
          left: 0;
          width: 190px;
          height: 40px;
          display: block;
          cursor: pointer;
          background: #586f80;
          border: 2px solid #fff;
          color: #fff;
          text-align: center;
          line-height: 40px;
          border-radius: 5px;
        }
      }
      a {
        display: inline-block;
        width: 100px;
        height: 40px;
        float: right;
        background: #586f80;
        border: 2px solid #fff;
        color: #fff;
        text-align: center;
        border-radius: 5px;
        line-height: 40px;
      }
    }
  }
  .hat-box {
    width: 100%;
    height: 150px;
    overflow: hidden;
    // border-bottom: 1px solid #586f80;

    .hat-select {
      margin-top: 10px;
      width: auto;
      height: 140px;
      // border-top: 2px solid #000;
      // background: #fff;
      overflow-x: scroll;
      overflow-y: hidden;
      .hat-border {
        width: 100px;
        height: 100px;
        display: table-cell;
        img {
          border-radius: 5px;
          margin: 10px;
          padding: 5px;
          box-sizing: border-box;
          border: 2px solid #fff;
          background: #d8e1f1;
          width: 100px;
          height: 100px;
        }
      }
    }
  }
  .download {
    height: 100%;
    width: 100%;
    background: rgba(1, 1, 1, 0.7);
    position: absolute;
    top: 0;
    left: 0;
    z-index: 12;
    text-align: center;
    img {
      width: 300px;
      height: 300px;
      border: 2px solid #fff;
      margin-top: 30px;
    }
    p {
      width: 6em;
      margin: 50px auto;
      color: #fff;
    }
  }
}
</style>
