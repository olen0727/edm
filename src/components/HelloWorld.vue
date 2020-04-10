<template>
  <div class="hello">
    <div id="myPanoViewer" class="viewer" style="width:100%;height:100vh;margin:auto;"></div>
    <div class="popup">
      <img src="../assets/images/popup.png" alt />
    </div>
  </div>
</template>

<script>
import $ from "jquery";
import * as PANOLENS from "panolens";
export default {
  name: "HelloWorld",
  data() {
    return {
      infospotItems: [
        {
          image: require("../assets/images/headline.png"),
          position2D: [50, 49],
          size: 600
        },
        {
          image: require("../assets/images/icon_01.png"),
          position2D: [40, 40],
          size: 200
        },
        {
          image: require("../assets/images/icon_02.png"),
          position2D: [25, 30],
          size: 200
        },
        {
          image: require("../assets/images/icon_03.png"),
          position2D: [55, 30],
          size: 200
        },
        {
          image: require("../assets/images/icon_04.png"),
          position2D: [75, 50],
          size: 200
        },
        {
          image: require("../assets/images/IMG_7623.png"),
          position2D: [0, 0],
          size: 600
        },
        {
          image: require("../assets/images/IMG_7624.png"),
          position2D: [0, 100],
          size: 600
        }
      ]
    };
  },
  methods: {
// 
// @positionConverter 2維座標轉為3維座標 (經緯度轉地心座標)
// @param { Number } pX,pY 元件於平面畫布上之座標 畫布起算點:左上角，元件定位點:中心
// @return [ Number ] y,z,x ::y(前後), z(上下), x(左右)
// 
    positionConverter(pX, pY) {
      let r, c, x, y, z, _px, _py;
      r = 800; //半徑:目視距離
      // c = r; //帶入Z軸時c 為對應位置, 目前先以r取代且不參考Z軸
      // R = Math.PI * 2 * r;
      // console.log("pXY", typeof pX, pY, R);
      if (pY >= 0 && pY < 50) {
        _py = pY;
        c = Math.sin((_py / 100) * Math.PI).toFixed(3) * r; // 不同於xy, z坐標最大只有半圈故 2PI/2
        z = Math.cos((_py / 100) * Math.PI).toFixed(3) * r;
      } else if (pY >= 50 && pY <= 100) {
        _py = 100 - pY;
        c = Math.sin((_py / 100) * Math.PI).toFixed(3) * r;
        z = -Math.cos((_py / 100) * Math.PI).toFixed(3) * r;
      }

      if (pX >= 0 && pX < 25) {
        _px = pX;
        x = Math.sin((_px / 100) * 2 * Math.PI).toFixed(3) * c;
        y = -Math.cos((_px / 100) * 2 * Math.PI).toFixed(3) * c;
      } else if (pX >= 25 && pX < 50) {
        _px = 50 - pX;
        x = Math.sin((_px / 100) * 2 * Math.PI).toFixed(3) * c;
        y = Math.cos((_px / 100) * 2 * Math.PI).toFixed(3) * c;
      } else if (pX >= 50 && pX < 75) {
        _px = pX - 50;
        x = -Math.sin((_px / 100) * 2 * Math.PI).toFixed(3) * c;
        y = Math.cos((_px / 100) * 2 * Math.PI).toFixed(3) * c;
      } else if (pX >= 75 && pX <= 100) {
        _px = 100 - pX;
        x = -Math.sin((_px / 100) * 2 * Math.PI).toFixed(3) * c;
        y = -Math.cos((_px / 100) * 2 * Math.PI).toFixed(3) * c;
      }
      // switch (pX) {
      //   case pX >= 0 && pX < 25:
      //     _px = pX;
      //     positionx = Math.sin(_px / R) * c;
      //     positiony = -Math.cos(_px / R) * c;
      //     console.log("1");
      //     break;
      //   case pX >= 25 && pX < 50:
      //     _px = 50 - pX;
      //     positionx = Math.sin(_px / R) * c;
      //     positiony = Math.cos(_px / R) * c;
      //     console.log("2");
      //     break;
      //   case pX >= 50 && pX < 75:
      //     _px = pX - 50;
      //     positionx = -Math.sin(_px / R) * c;
      //     positiony = Math.cos(_px / R) * c;
      //     console.log("3");
      //     break;
      //   case pX >= 75 && pX <= 100:
      //     _px = 100 - pX;
      //     positionx = -Math.sin(_px / R) * c;
      //     positiony = -Math.cos(_px / R) * c;
      //     console.log("4");
      //     break;
      // }
      // console.log(x, y, z);
      return [y, z, x];
      // return [positionx, positiony];
    }
  },
  mounted() {
    var container, panorama, viewer;
    // , infospot
    // console.log(this.infospotItems);

    container = document.querySelector("#myPanoViewer");
    panorama = new PANOLENS.ImagePanorama(
      require("../assets/images/edm_bg_block.png")
    );

    this.infospotItems.forEach(item => {
      const infospot = new PANOLENS.Infospot(item.size, item.image);
      infospot.position.set(...this.positionConverter(...item.position2D));
      infospot.addEventListener("click", function() {
        this.focus();
        $(".popup").show();
      });
      panorama.add(infospot);
    });
    $(".popup").on("click", function() {
      $(this).hide();
    });
    // console.log("panorama", panorama);

    // // infospots// infospots// infospots// infospots
    // infospot = new PANOLENS.Infospot(
    //   400,
    //   require("../assets/images/headline.png")
    // );
    // infospot.position.set(800, 0, 0); //z, x, y
    // infospot.setText("Default Infospot");
    // infospot.addEventListener("click", function() {
    //   this.focus();
    //   $(".popup").show();
    // });
    // console.log(infospot);

    // infospot2 = new PANOLENS.Infospot(
    //   400,
    //   require("../assets/images/icon_01.png")
    // );
    // infospot2.position.set(0, 0, -800); //y(前後), z(上下), x(左右)

    // infospot3 = new PANOLENS.Infospot(
    //   400,
    //   require("../assets/images/icon_03.png")
    // );
    // infospot3.position.set(0, 1000, 800);
    // // infospots// infospots// infospots// infospots

    // panorama.add(infospot);
    // panorama.add(infospot2);
    // panorama.add(infospot3);
    viewer = new PANOLENS.Viewer({
      container: container,
      autoHideInfospot: false,
      horizontalView: false
    });
    viewer.add(panorama);
    // console.log(viewer);

    viewer.panorama.children[0].focus();
    // viewer.panorama.children[0].getContainer();
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.popup {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  z-index: 1;
  display: none;
  img {
    max-width: 90%;
    max-height: 90%;
  }
}
</style>
