<template>
  <div>
    <div class="pdf_winbg" v-if="pdfshow"></div>
    <div class="pdf_wins" v-if="pdfshow">
      <div class="pdfimgbox">
        <div class="pdfimg" :style="{'width': loadedRatio+'%'}">
          <pdf
            :src="pdfurl"
            ref="myPdfComponent"
            @num-pages="pageCount=$event"
            @page-loaded="curPage=$event"
            @error="loadedError"
            :page="curPage"
          ></pdf>
        </div>
      </div>
      <div class="pdfbtnbox">
        <a class="pdfbtn" v-on:click="pdfPage(-1)" v-if="!(this.curPage === 1)">上一页</a>
        <a class="pdfbtn" v-on:click="pdfZoom(50)">放大</a>
        <a class="pdfbtn" v-on:click="pdfZoom(-50)">缩小</a>
        <a class="pdfbtn" v-on:click="pdfPage(1)" v-if="!(this.curPage === this.pageCount)">下一页</a>
        <a class="pdfbtn" v-on:click="closePdf()">关闭</a>
      </div>
    </div>
  </div>
</template>

<script>
import pdf from "vue-pdf";
export default {
  name: "tb-pdf",
  components: { pdf },
  props: {
    pdfurl: {
      type: String
    },
    pdfshow: {
	  type: Boolean,
	  default:false
    },
    handle: {
      type: Function,
      data: () => {}
    }
  },
  data() {
    return {
      pageCount: 1, // 总页数
      curPage: 1,
      loadedRatio: 100
    };
  },
  mounted() {},
  methods: {
    pdfZoom(ratio) {
      if (ratio < 0 && this.loadedRatio <= 100) return;
      this.loadedRatio += ratio;
    },
    pdfPage(step) {
      if (this.curPage + step <= 0) return;
      if (this.curPage === this.pageCount && step === 1) {
        return;
      }
      this.curPage += step;
    },
    closePdf() {
      this.handle("close");
    },
    // 加载失败
    loadedError() {
      this.$vux.toast.text("文件加载失败");
    }
  },
  watch: {
    pdfurl() {
      this.curPage = 1;
      this.loadedRatio = 100;
    }
  }
};
</script>

<style lang="scss" scoped>
.pdf_winbg {
  position: fixed;
  z-index: 10000;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #000;
  opacity: 0.8;
}

.pdf_wins {
  position: fixed;
  z-index: 10000;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  .pdfimgbox {
    width: 95%;
    position: relative;
    height: 12.8rem;
    overflow: scroll;
  }
  .pdfimg {
    position: absolute;
    top: 0;
    left: 0;
  }
  .pdfbtnbox {
    width: 90%;
    display: flex;
    font-size: 0.4267rem;
    padding-top: 0.5rem;
  }
  .pdfbtn:active {
    opacity: 0.5;
  }
  .pdfbtn {
    flex: 1;
    margin: 0.2667rem 0.1333rem;
    line-height: 0.8rem;
    background: rgba(255,255,255,.8);
    text-align: center;
    border-radius: 0.08rem;
  }
}
</style>
