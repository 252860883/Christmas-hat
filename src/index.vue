<template>
  <div id="app">
    <div class="img-top">

      <div class="img-border">
        <img class="img" :src="imgUrl"/>   
        <!-- 素材区 -->
        <div class="hat-con" >
          <div class="hat-border">
            <div class="del" @touchmove="moveBar($event)">||</div>
          </div>
          <img class="imghat" :src="require('./assets/hat1.png')" @touchstart="moveStart($event)" @touchmove="moveHat($event)" @touchend="moveEnd()"/>          
        </div>
      </div>

      <div class="button-group">
        <!-- accept属性限制上传文件类型 -->
        <input class="upload" type="file" @change="fileChange" accept="image/jpeg,image/png,image/gif">
        <a @click="drawCanvas">保存</a>
      </div>
      
    </div>
    <div class="hat-box">
    <div class="hat-select">
      <div class="hat-border" v-for="hat in hatLists"> 
        <img :src="hat">
      </div>
    </div>
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
        require("./assets/hat1.png"),
        require("./assets/hat1.png"),
        require("./assets/hat1.png"),
        require("./assets/hat1.png")
      ],
      sPosition: {
        x: 0,
        y: 0
      },
      dX: 0,
      dY: 0,
      moveX: 0,
      moveY: 0,
      scale: 1 //放大的尺寸
    };
  },
  mounted() {
    // var hammer = new Hammer(document.getElementById("container"));
  },
  methods: {
    fileChange(event) {
      let self = this;
      let reader = new FileReader();
      let img = event.target.files[0];
      // console.log(img);
      reader.readAsDataURL(img);
      reader.onloadend = function() {
        self.imgUrl = reader.result;
      };
    },
    drawCanvas() {
      // console.log(html2canvas);
      html2canvas(document.querySelector(".img-border")).then(canvas => {
        document.body.appendChild(canvas);
      });
    },
    moveStart(e) {
      this.sPosition = {
        x: e.touches[0].clientX,
        y: e.touches[0].clientY
      };
    },
    moveHat(e) {
      let moveDom = document.querySelector(".hat-con");
      this.moveX = e.touches[0].clientX - this.sPosition.x;
      this.moveY = e.touches[0].clientY - this.sPosition.y;
      moveDom.style.transform = `translateX(${this.dX +
        this.moveX}px) translateY(${this.dY + this.moveY}px) scale(${this.scale})`;
      moveDom.style.webkitTransform = `translateX(${this.dX +
        this.moveX}px) translateY(${this.dY + this.moveY}px) scale(${this.scale})`;
    },
    moveEnd() {
      this.dX += this.moveX;
      this.dY += this.moveY;
    },
    moveBar(e) {
      let moveDom = document.querySelector(".hat-con");
      console.log(moveDom.getBoundingClientRect().x,moveDom.getBoundingClientRect().width)
      this.scale =
        (e.touches[0].clientX - moveDom.getBoundingClientRect().x) /
        moveDom.getBoundingClientRect().width;
      moveDom.style.transform = `translateX(${this.dX}px) translateY(${this
        .dY}px) scale(${this.scale})`;
      moveDom.style.webkitTransform = `translateX(${this
        .dX}px) translateY(${this.dY}px) scale(${this.scale})`;
    }
  }
};
</script>

<style lang="scss">
body {
  margin: 0;
  padding: 0;
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 0;
  margin-top: 60px;
  padding: 0;
  .img-top {
    width: 300px;
    margin: 0 auto;
    .img-border {
      display: block;
      width: 300px;
      height: 300px;
      border: 1px solid #000;
      overflow: hidden;
      position: relative;
      .img {
        max-width: 300px;
        min-height: 300px;
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
            bottom: -8px;
            right: -8px;
            width: 20px;
            height: 20px;
            background: #000;
            color: #fff;
            border-radius: 50%;
            text-align: center;
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
        margin-right: 20px;
        &::after {
          content: "上传图片";
          position: absolute;
          top: 0;
          left: 0;
          width: 190px;
          height: 40px;
          display: block;
          cursor: pointer;
          background: #000;
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
        background: #000;
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
    border-bottom: 1px solid #000;
    .hat-select {
      margin-top: 30px;
      width: auto;
      height: 140px;
      border-top: 1px solid #000;
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
          border: 1px solid #000;
          width: 100px;
          height: 100px;
        }
      }
    }
  }
}
</style>
