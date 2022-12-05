<template>
  <h3>html编辑器</h3>
  <textarea class="textarea" v-model="htmlText" autofocus/>
  <h3>html预览</h3>
  <div class="invite_cls" ref="imageDom" id="imageDomId" v-html="htmlText">
  </div>
  <div>
    <button class="btn_text" @click="saveQRImg">生成图片</button>
  </div>

  <div v-if="imgSrc">
    <img class="btn_img" :src="imgSrc">
    <div>
      <button class="btn_text" @click="downloadEvt(imgSrc)">下载图片</button>
    </div>
  </div>
</template>
<script>
import html2canvas from "html2canvas";// 引入
export default {
  data() {
    return {
      imgSrc: '',
      htmlText: ` <div class="container">
    <div class="circle">hello</div>
  </div>  
  <style>
    .container {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 30vh;
    }
    .circle {
      height: 100px;
      width: 100px;
      line-height: 100px;
      border: 1px solid red;
      border-radius: 50%;
    }
  </style>`
    }
  },
  methods: {
    saveQRImg() {    // 保存二维码图片
      // 获取要生成图片的 DOM 元素
      let canvasDom = this.$refs.imageDom
      const options = {
        scale: 8, //按比例增加分辨率 (2=双倍)
        dpi: window.devicePixelRatio * 8, //设备像素比
        // width: width,  // 保存的图片的宽
        // height: height,  // 保存的图片的高
      }
      html2canvas(canvasDom, options).then((canvas) => {
        this.imgSrc = canvas.toDataURL("image/jpeg")// jpeg格式，也可以保存成png格式
      }).catch(function (error) {
        console.log(error, '转码失败')
      });
    },

    downloadEvt(url, fileName = '图片') {
      const el = document.createElement('a');
      el.style.display = 'none';
      el.setAttribute('target', '_blank');
      /**
        * download的属性是HTML5新增的属性
        * href属性的地址必须是非跨域的地址，如果引用的是第三方的网站或者说是前后端分离的项目(调用后台的接口)，这时download就会不起作用。
        * 此时，如果是下载浏览器无法解析的文件，例如.exe,.xlsx..那么浏览器会自动下载，但是如果使用浏览器可以解析的文件，比如.txt,.png,.pdf....浏览器就会采取预览模式
        * 所以，对于.txt,.png,.pdf等的预览功能我们就可以直接不设置download属性(前提是后端响应头的Content-Type: application/octet-stream，如果为application/pdf浏览器则会判断文件为 pdf ，自动执行预览的策略)
        */
      fileName && el.setAttribute('download', fileName);
      el.href = url;
      document.body.appendChild(el);
      el.click();
      document.body.removeChild(el);
    }
  }
}
</script>
<style>
.textarea {
  width: 100%;
  min-height: 29vh;
}

.invite_cls {
  border: 1px solid gray;
  min-height: 30vh;
}

.btn_text {
  margin: 20px 0;
}

.btn_img {
  width: 100%;
}
</style>