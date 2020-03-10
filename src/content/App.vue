<template>
  <div></div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      isAllLoad: true
    };
  },
  created() {
    this.checkImageLoad();
  },
  mounted() {
    // 复制原网页dom元素到minimap上
    if (this.isAllLoad) {
      this.copyDom();

      // console.log("wuwwww");
      // console.log(document.documentElement.clientHeight);
      // console.log(document.body.clientHeight);

      document.getElementById("now-screen").style.height =
        Number(document.documentElement.clientHeight) * 0.15 + "px";

      //监听滚动事件
      window.onscroll = this.scrollListener;
      // 调整浏览器大小时，也调整小屏幕大小
      window.onresize = function() {
        document.getElementById("now-screen").style.height =
          Number(document.documentElement.clientHeight) * 0.15 + "px";
      };
    }
  },
  methods: {
    copyDom() {
      console.log("copyDOM");
      let minimapDOM = document.createElement("div");
      minimapDOM.setAttribute("id", "minimap");
      document.body.appendChild(minimapDOM);

      let itm = document.body;
      // itm.removeChild(document.getElementById("minimap"));
      let cln = itm.cloneNode(true);
      console.log(cln);
      document.getElementById("minimap").appendChild(cln);

      let mapcontainer = document.createElement("div");
      mapcontainer.setAttribute("id", "mapcontainer");
      document.body.appendChild(mapcontainer);
      let nowScreen = document.createElement("div");
      nowScreen.setAttribute("id", "now-screen");
      document.getElementById("mapcontainer").appendChild(nowScreen);
      document.body.style.width = "85%";
    },

    scrollListener() {
      //总的缩放系数是0.15
      const zoom = 0.15;

      //计算出缩放系数和小屏幕滚动速度的关系，0.01是调试出的固定常量
      let screenZoom =
        document.documentElement.clientHeight / document.body.clientHeight -
        0.01;
      let mapZoom = zoom - screenZoom;

      // 当前小屏幕滚动
      document.getElementById("now-screen").style.marginTop = `${Number(
        document.documentElement.scrollTop
      ) * screenZoom}px`;
      // minimap滚动
      document.getElementById("minimap").style.top = `${-(
        Number(document.documentElement.scrollTop) * mapZoom
      )}px`;
    },
    checkImageLoad() {
      let imgList = document.getElementsByTagName("img"); //图片集合
      let imgCount = imgList.length; //图片总数
      let imgLoad = 0; //加载完成的图片数量
      console.log("imgCount:" + imgCount);
      for (let i = 0; i < imgCount; i++) {
        imgList[i].onload = () => {
          imgLoad++;
          console.log(imgLoad);
          if (imgLoad === imgCount) {
            this.loading = false;
            this.display = true;
            console.log("图片加载完成 展示组件");
            this.isAllLoad = true;
          }
        };
      }

      setTimeout(() => {
        if (!this.display) {
          this.loading = false;
          this.display = !this.display;
          console.log("超时 强制显示");
        }
      }, 5000);
    }
  }
};
</script>

<style scoped>
#mapcontainer {
  position: fixed;
  right: 0;
  top: 0;
  width: 15%;
  height: 100%;
  box-shadow: -5px 0 20px rgba(30, 30, 30, 0.08);
  overflow: hidden;
}

#now-screen {
  width: 100%;
  border: 4px solid rgba(255, 0, 0, 0.5);
  box-sizing: border-box;

  box-shadow: 0px 0px 100px rgba(30, 30, 30, 0.1);
}

#minimap {
  position: fixed;
  right: 0;
  top: 0;
  display: flex;
  justify-content: center;
  transform: scale(0.15);
  width: 100%;
  height: 100%;
  transform-origin: right top;
  background-color: #fff;
}
</style>
