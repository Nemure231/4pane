<script>
import 'splitpanes/dist/splitpanes.css'
import { Splitpanes, Pane } from 'splitpanes'
import { Cropper } from 'vue-advanced-cropper'
import { OnClickOutside } from '@vueuse/components'
import 'vue-advanced-cropper/dist/theme.compact.css';
import 'vue-advanced-cropper/dist/style.css';
import Modal from "./Modal.vue";
export default {
  components: { Splitpanes, Pane, Cropper, Modal, OnClickOutside },
  data() {
    return {
      // rotate: false,
      loadImgTime: 0,
      restart: false,
      percent: 100,
      loading: false,
      modal: false,
      urlImg: '',
      selectExample: '',
      pickPane: {
        id: 0,
        paneName: 'Free Move',
      },
      dropdown: false,
      result: {
        pane: [
          {
            size: null,
          },
          {
            size: null,
          },
          {
            size: null,
          },
          {
            size: null,
          },
          {
            size: null,
          },
          {
            size: null,
          },
          {
            size: null,
          }
        ]
      },
      example: [
        {
          id: 1,
          name: 'Syaro',
          url: 'img/example/1.jpg'
        },
        {
          id: 2,
          name: 'Hakase',
          url: 'img/example/2.jpg'
        },
        {
          id: 3,
          name: 'Renge',
          url: 'img/example/3.jpg'
        }
      ],
      coordinates: {
        width: 0,
        height: 0,
        left: 0,
        top: 0,
      },
      image: {
        src: null,
        type: null
      },
      cropedImg: null,
      canvas: [
        {
          w: 0,
          h: 0,
        },
        {
          w: 0,
          h: 0,
        },
        {
          w: 0,
          h: 0,
        },
        {
          w: 0,
          h: 0,
        },
        {
          w: 0,
          h: 0,
        },
        {
          w: 0,
          h: 0,
        },

      ],
      pane: [
        {
          size: 50
        },
        {
          size: 33.33333333333333
        },
        {
          size: 33.33333333333333
        },
        {
          size: 33.33333333333333
        },
        {
          size: 0
        },
        {
          size: 0
        },
        {
          size: 0
        },
        {
          size: 0
        }
      ],
    }
  },
  mounted() {
    this.setDefaultPane()
  },
  updated() {
    this.setCanvas()
  },
  methods: {
    setDefaultPane() {
      this.pane[5].size = this.pane[2].size
      this.pane[4].size = this.pane[2].size
      this.pane[6].size = this.pane[2].size
      this.pane[7].size = this.pane[2].size
    },
    openModal() {
      return this.modal = !this.modal;
    },
    closeModal() {
      return this.modal = false;
    },
    getLocation() {
      return window.location.href;
    },
    getMimeType(file, fallback = null) {
      const byteArray = (new Uint8Array(file)).subarray(0, 4);
      let header = '';
      for (let i = 0; i < byteArray.length; i++) {
        header += byteArray[i].toString(16);
      }
      switch (header) {
        case "89504e47":
          return "image/png";
        case "47494638":
          return "image/gif";
        case "ffd8ffe0":
        case "ffd8ffe1":
        case "ffd8ffe2":
        case "ffd8ffe3":
        case "ffd8ffe8":
          return "image/jpeg";
        default:
          return fallback;
      }
    },
    resizeImage(url, width, height, x, y, callback) {
      var canvas = document.createElement("canvas");
      var context = canvas.getContext("2d");
      canvas.width = width;
      canvas.height = height;

      const imageObj = new Image();
      imageObj.src = url;
      imageObj.crossOrigin = "anonymous"; //WARNING!
      imageObj.onload = () => {

        context.drawImage(imageObj, x, y, width, height, 0, 0, width, height);

        const url = canvas.toDataURL();
        callback(url);
      };
    },
    setCanvas0() {
      this.resizeImage(this.cropedImg, this.coordinates.width, this.coordinates.height, 0, 0, (url) => {
        const canvas = this.$refs.canvas0;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });
    },
    setCanvas1() {
      const yCanvas = this.coordinates.height / 3
      const checkYCanvas = this.restart ? this.canvas[0].h : yCanvas
      const checkCoorW = this.restart ? this.canvas[0].w : this.coordinates.width
      const checkCoorH = this.restart ? this.canvas[1].h : this.coordinates.height

      this.resizeImage(this.cropedImg, checkCoorW, checkCoorH, 0, checkYCanvas, (url) => {
        const canvas = this.$refs.canvas1;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });
    },
    setCanvas2() {
      const yCanvas = this.coordinates.height / 3 * 2
      const checkYCanvas = this.restart ? this.canvas[0].h + this.canvas[1].h : yCanvas
      const checkCoorW = this.restart ? this.canvas[2].w : this.coordinates.width
      const checkCoorH = this.restart ? this.canvas[2].h : this.coordinates.height

      this.resizeImage(this.cropedImg, checkCoorW, checkCoorH, 0, checkYCanvas, (url) => {
        const canvas = this.$refs.canvas2;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });
    },
    setCanvas3() {
      const xCanvas = this.coordinates.width / 2
      const checkXCanvas = this.restart ? this.canvas[0].w : xCanvas
      const checkCoorW = this.restart ? this.canvas[3].w : this.coordinates.width
      const checkCoorH = this.restart ? this.canvas[3].h : this.coordinates.height

      this.resizeImage(this.cropedImg, checkCoorW, checkCoorH, checkXCanvas, 0, (url) => {
        const canvas = this.$refs.canvas3;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });
    },
    setCanvas4() {
      const yCanvas = this.coordinates.height / 3
      const xCanvas = this.coordinates.width / 2

      const checkXCanvas = this.restart ? this.canvas[0].w : xCanvas
      const checkYCanvas = this.restart ? this.canvas[3].h : yCanvas
      const checkCoorW = this.restart ? this.canvas[3].w : this.coordinates.width
      const checkCoorH = this.restart ? this.canvas[4].h : this.coordinates.height

      this.resizeImage(this.cropedImg, checkCoorW, checkCoorH, checkXCanvas, checkYCanvas, (url) => {
        const canvas = this.$refs.canvas4;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });
    },
    setCanvas5() {
      const yCanvas = this.coordinates.height / 3 * 2
      const xCanvas = this.coordinates.width / 2
      const checkXCanvas = this.restart ? this.canvas[0].w : xCanvas
      const checkYCanvas = this.restart ? this.canvas[3].h + this.canvas[4].h : yCanvas
      const checkCoorW = this.restart ? this.canvas[5].w : this.coordinates.width
      const checkCoorH = this.restart ? this.canvas[5].h : this.coordinates.height

      this.resizeImage(this.cropedImg, checkCoorW, checkCoorH, checkXCanvas, checkYCanvas, (url) => {
        const canvas = this.$refs.canvas5;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });
    },

    resizeCanvasY1() {
      this.loading = true
      this.restart = true
      const startLoadImgTime = new Date().getTime();

      this.resizeImage(this.cropedImg, this.canvas[0].w, this.canvas[0].h, 0, 0, (url) => {
        const canvas = this.$refs.canvas0;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });

      this.resizeImage(this.cropedImg, this.canvas[0].w, this.canvas[1].h, 0, this.canvas[0].h, (url) => {
        const canvas = this.$refs.canvas1;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });
      const yCanvas2 = this.canvas[0].h + this.canvas[1].h
      this.resizeImage(this.cropedImg, this.canvas[2].w, this.canvas[2].h, 0, yCanvas2, (url) => {
        const canvas = this.$refs.canvas2;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });
      setTimeout(() => {
        this.loading = false
      }, this.loadImgTime);

    },

    resizeCanvasY2() {
      this.loading = true
      this.restart = true
      const startLoadImgTime = new Date().getTime();

      this.resizeImage(this.cropedImg, this.canvas[3].w, this.canvas[3].h, this.canvas[0].w, 0, (url) => {
        const canvas = this.$refs.canvas3;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });

      this.resizeImage(this.cropedImg, this.canvas[3].w, this.canvas[4].h, this.canvas[0].w, this.canvas[3].h, (url) => {
        const canvas = this.$refs.canvas4;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });

      const yCanvas5 = this.canvas[3].h + this.canvas[4].h
      this.resizeImage(this.cropedImg, this.canvas[5].w, this.canvas[5].h, this.canvas[0].w, yCanvas5, (url) => {
        const canvas = this.$refs.canvas5;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });
      setTimeout(() => {
        this.loading = false
      }, this.loadImgTime);
    },
    resizeCanvasX() {
      this.loading = true
      this.restart = true

      const startLoadImgTime = new Date().getTime();


      this.resizeImage(this.cropedImg, this.canvas[0].w, this.canvas[0].h, 0, 0, (url) => {
        const canvas = this.$refs.canvas0;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });
      this.resizeImage(this.cropedImg, this.canvas[0].w, this.canvas[1].h, 0, this.canvas[0].h, (url) => {
        const canvas = this.$refs.canvas1;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });
      const yCanvas2 = this.canvas[0].h + this.canvas[1].h
      this.resizeImage(this.cropedImg, this.canvas[2].w, this.canvas[2].h, 0, yCanvas2, (url) => {
        const canvas = this.$refs.canvas2;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });

      this.resizeImage(this.cropedImg, this.canvas[3].w, this.canvas[3].h, this.canvas[0].w, 0, (url) => {
        const canvas = this.$refs.canvas3;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });
      this.resizeImage(this.cropedImg, this.canvas[3].w, this.canvas[4].h, this.canvas[0].w, this.canvas[3].h, (url) => {
        const canvas = this.$refs.canvas4;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });
      const yCanvas5 = this.canvas[3].h + this.canvas[4].h
      this.resizeImage(this.cropedImg, this.canvas[5].w, this.canvas[5].h, this.canvas[0].w, yCanvas5, (url) => {
        const canvas = this.$refs.canvas5;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
          this.loadImgTime = new Date().getTime() - startLoadImgTime;
        };
      });
      setTimeout(() => {
        this.loading = false
      }, this.loadImgTime);
    },
    getBase64(imgUrl, callback) {
      var img = new Image();
      // onload fires when the image is fully loadded, and has width and height
      img.onload = function () {
        var canvas = document.createElement("canvas");
        canvas.width = img.width;
        canvas.height = img.height;
        var ctx = canvas.getContext("2d");
        ctx.drawImage(img, 0, 0);
        var dataURL = canvas.toDataURL("image/png"),
          dataURL = dataURL.replace(/^data:image\/(png|jpg);base64,/, "");
        callback(dataURL); // the base64 string
      };

      // set attributes and src 
      img.setAttribute('crossOrigin', 'anonymous'); //
      img.src = imgUrl;

    },
    async upload() {
      // If url exist in input
      if (this.urlImg) {
        // Get the result from link validation
        const checkUrlType = this.checkUrlImg(this.urlImg);
        // And check the result
        if (checkUrlType === true) {
          // Make a fetch to check if the website have CORS policy or not
          await fetch(this.urlImg, {
            method: 'GET',
            headers: {
            },
          })
            .then(response => response.blob())
            .then(data => {
              this.image.src = this.urlImg
            })
            .catch((error) => {
              alert('The url of this image is protected or blocked by CORS policy, if you want this image just download it and choose again.')
            });
        } else {
          // Else return the alert message
          alert('The url only accept https, and with .jpg, .jpeg, and .png extension in the end of url!')
        }
        // Else people select form their local storage
      } else {
        this.$refs.file.click()
      }
    },
    checkUrlImg(url) {
      if (url.match(/^https:?:\/\/.+\/.+$/) && url.match(/\.(jpeg|jpg|png)$/) !== null) {
        return true
      } else {
        return false
      }
    },
    loadImage(event) {
      const { files } = event.target;
      // Ensure that you have a file before attempting to read it
      if (files && files[0]) {
        const fileType = files[0]['type'];
        const fileSize = files[0]['size'];
        const validImageTypes = ['image/gif', 'image/jpeg', 'image/png'];
        // TYPE IMAGE VALIDATION
        if (!validImageTypes.includes(fileType)) {
          alert('Please, choose the right format image! The avaiable format is JPG, JPEG, and PNG!');
        }
        //SIZE IMAGE VALIDATION
        else if (fileSize >= 1045301 * 2) {
          alert('The maximum size image to upload is 2MB, please reduce your image size before upload again!');
        }
        else {

          // 1. Revoke the object URL, to allow the garbage collector to destroy the uploaded before file
          if (this.image.src) {
            URL.revokeObjectURL(this.image.src)
          }
          // 2. Create the blob link to the file to optimize performance:
          const blob = URL.createObjectURL(files[0]);

          // 3. The steps below are designated to determine a file mime type to use it during the 
          // getting of a cropped image from the canvas. You can replace it them by the following string, 
          // but the type will be derived from the extension and it can lead to an incorrect result:
          //
          // this.image = {
          //    src: blob;
          //    type: files[0].type
          // }

          // Create a new FileReader to read this image binary data
          const reader = new FileReader();
          // Define a callback function to run, when FileReader finishes its job
          reader.onload = (e) => {
            // Note: arrow function used here, so that "this.image" refers to the image of Vue component
            this.image = {
              // Set the image source (it will look like blob:http://example.com/2c5270a5-18b5-406e-a4fb-07427f5e7b94)
              src: blob,
              // Determine the image type to preserve it during the extracting the image from canvas:
              type: this.getMimeType(e.target.result, files[0].type),
            };
          };
          // Start the reader job - read file as a data url (base64 format)
          reader.readAsArrayBuffer(files[0]);
        }
      }
    },

    getCrop() {
      const { coordinates, canvas, } = this.$refs.cropper.getResult();
      this.coordinates = coordinates;
      this.cropedImg = canvas.toDataURL();
    },
    rotateImg(angle) {
      this.$refs.cropper.rotate(angle);
    },
    setCanvas() {
      this.setCanvas0();
      this.setCanvas1();
      this.setCanvas2();
      this.setCanvas3();
      this.setCanvas4();
      this.setCanvas5();
    },
    dlCanvas(name, refCanvas) {
      var canvas = this.$refs[refCanvas]
      var image = canvas.toDataURL();
      var tmpLink = document.createElement('a');
      tmpLink.download = `${name}.png`;
      tmpLink.href = image;

      document.body.appendChild(tmpLink);
      tmpLink.click();
      document.body.removeChild(tmpLink);
    },
    changePane() {
      if (this.pickPane.id == 0) {
        this.zeroResult();
        this.pickPane.paneName = 'Free Move'
      }
      if (this.pickPane.id == 1) {
        this.oneResult();
        this.pickPane.paneName = '2 Horizontal Panels'
      }
      if (this.pickPane.id == 2) {
        this.twoResult();
        this.pickPane.paneName = '2 Vertical Panels'
      }
      if (this.pickPane.id == 3) {
        this.threeResult();
        this.pickPane.paneName = '3 Horizontal Panels'
      }
      // if (this.pickPane.id == 4) {
      //   this.fourResult();
      //   this.pickPane.paneName = '3 Panel Vertical'
      // }
      if (this.pickPane.id == 5) {
        this.fiveResult();
        this.pickPane.paneName = '4 Panels'
      }
      if (this.pickPane.id == 6) {
        this.sixResult();
        this.pickPane.paneName = '3 Cols Panels'
      }
      if (this.pickPane.id == 7) {
        this.sevenResult();
        this.pickPane.paneName = '4 Horizontal Cols Panels'
      }
      if (this.pickPane.id == 8) {
        this.eightResult();
        this.pickPane.paneName = '3 Horizontal Rows Panels'
      }
    },
    zeroResult() {
      this.result.pane[0].size = null
      this.result.pane[1].size = null
      this.result.pane[2].size = null
      this.result.pane[3].size = null
      this.result.pane[4].size = null
      this.result.pane[5].size = null
      this.result.pane[6].size = null
    },
    oneResult() {
      this.result.pane[0].size = 0
      this.result.pane[1].size = 50
      this.result.pane[2].size = null
      this.result.pane[3].size = 0
      this.result.pane[4].size = 50
      this.result.pane[5].size = null
      this.result.pane[6].size = 100
    },
    twoResult() {
      this.result.pane[0].size = 0
      this.result.pane[1].size = 0
      this.result.pane[2].size = null
      this.result.pane[3].size = 0
      this.result.pane[4].size = 0
      this.result.pane[5].size = null
      this.result.pane[6].size = 50
    },
    threeResult() {
      this.result.pane[0].size = 33.33333333333333
      this.result.pane[1].size = null
      this.result.pane[2].size = 33.33333333333333

      this.result.pane[3].size = 33.33333333333333
      this.result.pane[4].size = null
      this.result.pane[5].size = 33.33333333333333
      this.result.pane[6].size = 100
    },
    // fourResult() {
    //   this.result.pane[0].size = 0
    //   this.result.pane[1].size = null
    //   this.result.pane[2].size = 0

    //   this.result.pane[3].size = 33.33333333333333
    //   this.result.pane[4].size = null
    //   this.result.pane[5].size = 33.33333333333333
    //   this.result.pane[6].size = 66.66666666666666
    // },
    fiveResult() {
      this.result.pane[0].size = 0
      this.result.pane[1].size = 50
      this.result.pane[2].size = null
      this.result.pane[3].size = 0
      this.result.pane[4].size = 50
      this.result.pane[5].size = null
      this.result.pane[6].size = 50
    },
    sixResult() {
      this.result.pane[0].size = 0
      this.result.pane[1].size = 0
      this.result.pane[2].size = null
      this.result.pane[3].size = 0
      this.result.pane[4].size = 50
      this.result.pane[5].size = null
      this.result.pane[6].size = 50
    },
    sevenResult() {
      this.result.pane[0].size = 0
      this.result.pane[1].size = null
      this.result.pane[2].size = 0
      this.result.pane[3].size = 33.33333333333333
      this.result.pane[4].size = null
      this.result.pane[5].size = 33.33333333333333
      this.result.pane[6].size = 66.66666666666666
    },
    eightResult() {
      this.result.pane[0].size = 0
      this.result.pane[1].size = 50
      this.result.pane[2].size = null
      this.result.pane[3].size = 0
      this.result.pane[4].size = 50
      this.result.pane[5].size = null
      this.result.pane[6].size = 100
    },
    // WIP
    // rotatePane() {
    //   this.rotate = !this.rotate

    //   var ctx = document.getElementById("canvas1").getContext("2d");

    //   // prep canvas for next actions
    //   ctx.translate(75, 75);                   // translate to canvas center
    //   ctx.rotate(Math.PI * 0.5);                 // add rotation transform
    //   ctx.globalCompositeOperation = "copy";   // set comp. mode to "copy"

    //   ctx.drawImage(ctx.canvas, 0, 0, this.canvas[0].w, this.canvas[0].h, -75, -75, this.canvas[0].w, this.canvas[0].h);
    // }
  },
  unmounted() {
    if (this.image.src) {
      URL.revokeObjectURL(this.image.src)
    }
  },
  computed: {
    isCroped() {
      return this.cropedImg ? 'block' : 'hidden';
    },
    isUploaded() {
      return this.image.src ? 'block' : 'hidden';
    },
    isBg() {
      return this.image.src ? '' : 'bg-[#E8F9FD]'
    },
    isMargin() {
      return this.image.src ? 'mt-44' : 'mt-32'
    },
    isLoading() {
      return this.loading ? 'block' : 'hidden'
    },
    isSizePane012() {
      return this.pane[2].size < 100 ? this.pane[3].size : this.pane[2].size
    },
    isSizePane345() {
      return this.pane[1].size < 100 ? this.pane[3].size : this.pane[1].size
    },
    isMinSizePane0() {
      return this.pickPane.id == 0 ? 0 : this.result.pane[0].size
    },
    isMaxSizePane0() {
      return this.pickPane.id == 0 ? 100 : this.result.pane[0].size
    },
    isMinSizePane1() {
      return this.pickPane.id == 0 ? 0 : this.result.pane[1].size
    },
    isMaxSizePane1() {
      return this.pickPane.id == 0 ? 100 : this.result.pane[1].size
    },
    isMinSizePane2() {
      return this.pickPane.id == 0 ? 0 : this.result.pane[2].size
    },
    isMaxSizePane2() {
      return this.pickPane.id == 0 ? 100 : this.result.pane[2].size
    },
    isMinSizePane3() {
      return this.pickPane.id == 0 ? 0 : this.result.pane[3].size
    },
    isMaxSizePane3() {
      return this.pickPane.id == 0 ? 100 : this.result.pane[3].size
    },
    isMinSizePane4() {
      return this.pickPane.id == 0 ? 0 : this.result.pane[4].size
    },
    isMaxSizePane4() {
      return this.pickPane.id == 0 ? 100 : this.result.pane[4].size
    },
    isMinSizePane5() {
      return this.pickPane.id == 0 ? 0 : this.result.pane[5].size
    },
    isMaxSizePane5() {
      return this.pickPane.id == 0 ? 100 : this.result.pane[5].size
    },
    isMinSizePane6() {
      if (this.pickPane.id == 0) {
        return 0
      } else if (this.pickPane.id == 8) {
        return 0
      } else {
        return this.result.pane[6].size
      }
    },
    isMaxSizePane6() {
      if (this.pickPane.id == 0) {
        return 100
      } else if (this.pickPane.id == 8) {
        return 50
      } else {
        return this.result.pane[6].size
      }
    },
    isMinSizePane6_2() {
      if (this.pickPane.id == 0) {
        return 0
      } else if (this.pickPane.id == 8) {
        return 50
      } else {
        return this.result.pane[6].size
      }
    },
    isMaxSizePane6_2() {
      if (this.pickPane.id == 0) {
        return 100
      } else if (this.pickPane.id == 8) {
        return 100
      } else {
        return this.result.pane[6].size
      }
    },
    isWidthCanvas0() {
      return this.canvas[0].w = this.restart ? (this.coordinates.width * this.pane[0].size / this.percent) : (this.coordinates.width / 2)
    },
    isHeightCanvas0() {
      return this.canvas[0].h = this.coordinates.height * this.pane[2].size / this.percent
    },
    isWidthCanvas1() {
      return this.canvas[1].w = this.restart ? (this.coordinates.width * this.pane[0].size / this.percent) : (this.coordinates.width / 2)
    },
    isHeightCanvas1() {
      return this.canvas[1].h = this.coordinates.height * this.pane[4].size / this.percent
    },
    isWidthCanvas2() {
      return this.canvas[2].w = this.restart ? (this.coordinates.width * this.pane[0].size / this.percent) : (this.coordinates.width / 2)
    },
    isHeightCanvas2() {
      return this.canvas[2].h = this.coordinates.height * this.pane[5].size / this.percent
    },
    isWidthCanvas3() {
      return this.canvas[3].w = this.coordinates.width * (this.percent - this.pane[0].size) / this.percent
    },
    isHeightCanvas3() {
      return this.canvas[3].h = this.coordinates.height * this.pane[1].size / this.percent
    },
    isWidthCanvas4() {
      return this.canvas[4].w = this.coordinates.width * (this.percent - this.pane[0].size) / this.percent
    },
    isHeightCanvas4() {
      return this.canvas[4].h = this.coordinates.height * this.pane[7].size / this.percent
    },
    isWidthCanvas5() {
      return this.canvas[5].w = this.coordinates.width * (this.percent - this.pane[0].size) / this.percent
    },
    isHeightCanvas5() {
      return this.canvas[5].h = this.coordinates.height * this.pane[6].size / this.percent
    },
  }
}
</script>
<template>

  <main class="w-full flex flex-wrap  flex-col justify-center items-center">
    <section class="flex-1 mt-12 relative w-full">
      <div class="flex justify-center flex-wrap">
        <div class="relative flex-1 2xl:max-w-7xl lg:max-w-5xl md:max-w-3xl sm:max-w-xl max-w-max lg:mx-0 md:mx-0 mx-3">
          <div
            class=" flex lg:gap-12 md:gap-12 sm:gap-14 gap-6 justify-center lg:flex-row md:flex-row flex-col flex-wrap items-center md:mx-3 mx-0">

            <div :class="isBg"
              class="lg:flex-1 md:flex-1 flex-none rounded-xl 2xl:h-[25rem] 2xl:w-[20rem] lg:h-[20rem] lg:w-[18rem] md:w-72 md:h-72 w-full sm:h-64 h-52">
              <div
                class="flex h-full flex-col items-center justify-center lg:w-full md:w-full sm:w-full w-[18rem] mx-auto">
                <cropper ref="cropper" :src="image.src" :stencil-props="{
                  // WIP
                  // aspectRatio: 16 / 11,
                  // aspectRatio: 11 / 16,
                  aspectRatio: 1 / 1,
                
                }">
                </cropper>
              </div>
              <div
                class="lg:mt-3 md:mt-3 sm:mt-3 mt-6 lg:text-base  flex-wrap md:text-base sm:text-sm text-xs flex flex-row gap-3 lg:justify-start md:justify-start justify-center items-center">
                <select @change="image.src = selectExample" v-model="selectExample" name="example"
                  class="text-gray-500 border lg:w-1/5 w-full border-gray-400 rounded-md px-3 lg:py-1.5 md:py-1.5 py-2 focus:outline-none">
                  <option selected value="">Example</option>
                  <option v-text="ex.name" v-for="ex in example" :key="ex.id" :value="`${getLocation()}${ex.url}`">
                  </option>
                </select>

                <div class="relative lg:w-1/3 w-full">
                  <div class="flex items-center">
                    <input v-model="urlImg"
                      class="relative border w-full border-gray-400 rounded-md pl-3 lg:pr-14 md:pr-14 pr-14 lg:py-1.5 md:py-1.5 py-2 focus:outline-none"
                      type="url" placeholder="Link/Local Image ....">

                    <button
                      class="absolute  right-1 font-semibold lg:px-3  lg:py-1 md:px-3 md:py-1 px-3 py-1 text-white  rounded-md bg-[#0AA1DD]"
                      @click="upload()">
                      <input class="hidden" type="file" ref="file" id="img-target" @change="loadImage($event)"
                        accept="image/*">
                      <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
                        aria-hidden="true" role="img" class="w-5 h-5" preserveAspectRatio="xMidYMid meet"
                        viewBox="0 0 256 256">
                        <path fill="#ffffff"
                          d="M240 136v64a16 16 0 0 1-16 16H32a16 16 0 0 1-16-16v-64a16 16 0 0 1 16-16h48a8 8 0 0 1 0 16H32v64h192v-64h-48a8 8 0 0 1 0-16h48a16 16 0 0 1 16 16ZM85.7 77.7L120 43.3V128a8 8 0 0 0 16 0V43.3l34.3 34.4a8.2 8.2 0 0 0 11.4 0a8.1 8.1 0 0 0 0-11.4l-48-48a8.1 8.1 0 0 0-11.4 0l-48 48a8.1 8.1 0 0 0 11.4 11.4ZM200 168a12 12 0 1 0-12 12a12 12 0 0 0 12-12Z">
                        </path>
                      </svg>
                    </button>

                  </div>
                </div>
                <div class="flex flex-row gap-x-3 lg:w-auto  w-full">
                  <button @click="getCrop" :class="isUploaded"
                    class="flex-1 lg:w-auto w-1/2 inline-flex items-center justify-center font-semibold lg:px-3 lg:py-1.5 md:px-3 md:py-1.5 px-0 py-1.5 text-white text-sm rounded-md bg-[#0AA1DD]">
                    <svg class="w-5 h-5 mx-2" xmlns="http://www.w3.org/2000/svg"
                      xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img"
                      preserveAspectRatio="xMidYMid meet" viewBox="0 0 24 24">
                      <path fill="#ffffff"
                        d="M17 15V7H9V5h8q.825 0 1.413.588Q19 6.175 19 7v8Zm0 8v-4H7q-.825 0-1.412-.587Q5 17.825 5 17V7H1V5h4V1h2v16h16v2h-4v4Z">
                      </path>
                    </svg>
                    Crop
                  </button>

                  <button :class="isCroped" @click="openModal()"
                    class="flex-1 lg:w-auto w-1/2 inline-flex items-center justify-center font-semibold lg:px-3 lg:py-1.5 md:px-3 md:py-1.5 px-0 py-1.5 text-white text-sm rounded-md bg-[#0AA1DD]">
                    <svg class="w-5 h-5 mx-2" xmlns="http://www.w3.org/2000/svg"
                      xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img"
                      preserveAspectRatio="xMidYMid meet" viewBox="0 0 32 32">
                      <path fill="#ffffff"
                        d="M28 4H4a2 2 0 0 0-2 2v20a2 2 0 0 0 2 2h24a2 2 0 0 0 2-2V6a2 2 0 0 0-2-2ZM4 6h16v20H4Zm24 20h-6V6h6Z">
                      </path>
                    </svg>
                    Panel
                  </button>
                </div>
              </div>
            </div>
            <div :class="isMargin"
              class="flex-none text-center bg-[#E8F9FD] relative lg:mt-0 md:mt-0 lg:w-[18rem] lg:h-[18rem] md:w-64 md:h-64 w-60 h-60">
              <template v-if="this.cropedImg">
                <span class="absolute -top-7 right-0 text-black rounded text-sm font-semibold py-0.5 px-3 bg-[#E8F9FD]"
                  v-text="pickPane.paneName"></span>
                <div class="absolute bg-cover bg-center inset-0 top-2 flex justify-center items-center">
                  <svg :class="isLoading" class="animate-spin h-12 w-12 text-[#2155cd]"
                    xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor"
                      d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
                    </path>
                  </svg>
                </div>
                <splitpanes
                  class="bg-cover bg-[#E8F9FD] border border-[#E8F9FD] lg:w-[18rem] lg:h-[18rem] md:w-64 md:h-64 w-60 h-60"
                  @resized="pane[0].size = $event[0].size, resizeCanvasX()" :style="{
                    backgroundImage: 'url(' + cropedImg + ')',
                    backgroundRepeat: 'no-repeat',
                  }">
                  <pane :min-size="isMinSizePane6" :max-size="isMaxSizePane6">
                    <splitpanes
                      @resized="pane[5].size = $event[2].size, pane[4].size = $event[1].size, pane[2].size = $event[0].size, resizeCanvasY1()"
                      horizontal>
                      <pane :min-size="isMinSizePane0" :max-size="isMaxSizePane0" :size="isSizepane012"
                        class=" group hover:bg-blue-500/20 relative ">
                        <button @click="dlCanvas('4pane_img_1', 'canvas0')" class="justify-center group-hover:block hidden items-center bg-white lg:p-2 md:p-2 p-1.5  rounded-br-md absolute 
                        left-0">
                          <svg class="lg:w-5 lg:h-5 md:h-5 md:w-5 w-4 h-4 fill-[#2155CD]"
                            xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
                            aria-hidden="true" role="img" preserveAspectRatio="xMidYMid meet" viewBox="0 0 256 256">
                            <path
                              d="M80.3 115.7a8 8 0 0 1 11.4-11.3l28.3 28.3V40a8 8 0 0 1 16 0v92.7l28.3-28.3a8 8 0 0 1 11.4 11.3l-42 42a8.2 8.2 0 0 1-11.4 0ZM216 144a8 8 0 0 0-8 8v56H48v-56a8 8 0 0 0-16 0v56a16 16 0 0 0 16 16h160a16 16 0 0 0 16-16v-56a8 8 0 0 0-8-8Z">
                            </path>
                          </svg>
                        </button>
                      </pane>
                      <pane :min-size="isMinSizePane1" :max-size="isMaxSizePane1"
                        class=" group hover:bg-blue-500/20 relative " :size="isSizepane012">
                        <button @click="dlCanvas('4pane_img_2', 'canvas1')" class="group-hover:flex justify-center items-center hidden bg-white lg:p-2 md:p-2 p-1.5  rounded-br-md absolute 
                      left">
                          <svg class="lg:w-5 lg:h-5 md:h-5 md:w-5 w-4 h-4 fill-[#2155CD]"
                            xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
                            aria-hidden="true" role="img" preserveAspectRatio="xMidYMid meet" viewBox="0 0 256 256">
                            <path
                              d="M80.3 115.7a8 8 0 0 1 11.4-11.3l28.3 28.3V40a8 8 0 0 1 16 0v92.7l28.3-28.3a8 8 0 0 1 11.4 11.3l-42 42a8.2 8.2 0 0 1-11.4 0ZM216 144a8 8 0 0 0-8 8v56H48v-56a8 8 0 0 0-16 0v56a16 16 0 0 0 16 16h160a16 16 0 0 0 16-16v-56a8 8 0 0 0-8-8Z">
                            </path>
                          </svg>
                        </button>
                      </pane>

                      <pane :min-size="isMinSizePane2" :max-size="isMaxSizePane2"
                        class="group hover:bg-blue-500/20 relative" :size="isSizepane012">
                        <button @click="dlCanvas('4pane_img_3', 'canvas2')" class="group-hover:flex justify-center items-center hidden bg-white lg:p-2 md:p-2 p-1.5  rounded-br-md absolute 
                      left">
                          <svg class="lg:w-5 lg:h-5 md:h-5 md:w-5 w-4 h-4 fill-[#2155CD]"
                            xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
                            aria-hidden="true" role="img" preserveAspectRatio="xMidYMid meet" viewBox="0 0 256 256">
                            <path
                              d="M80.3 115.7a8 8 0 0 1 11.4-11.3l28.3 28.3V40a8 8 0 0 1 16 0v92.7l28.3-28.3a8 8 0 0 1 11.4 11.3l-42 42a8.2 8.2 0 0 1-11.4 0ZM216 144a8 8 0 0 0-8 8v56H48v-56a8 8 0 0 0-16 0v56a16 16 0 0 0 16 16h160a16 16 0 0 0 16-16v-56a8 8 0 0 0-8-8Z">
                            </path>
                          </svg>
                        </button>
                      </pane>
                    </splitpanes>
                  </pane>
                  <pane :min-size="isMinSizePane6_2" :max-size="isMaxSizePane6_2">
                    <splitpanes
                      @resized="pane[6].size = $event[2].size, pane[7].size = $event[1].size, pane[1].size = $event[0].size, resizeCanvasY2()"
                      horizontal>
                      <pane :min-size="isMinSizePane3" :max-size="isMaxSizePane3"
                        class=" group hover:bg-blue-500/20 relative " :size="isSizepane345">
                        <button @click="dlCanvas('4pane_img_4', 'canvas3')" class="group-hover:flex justify-center items-center hidden bg-white lg:p-2 md:p-2 p-1.5  rounded-bl-md absolute 
                      right-0">
                          <svg class="lg:w-5 lg:h-5 md:h-5 md:w-5 w-4 h-4 fill-[#2155CD]"
                            xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
                            aria-hidden="true" role="img" preserveAspectRatio="xMidYMid meet" viewBox="0 0 256 256">
                            <path
                              d="M80.3 115.7a8 8 0 0 1 11.4-11.3l28.3 28.3V40a8 8 0 0 1 16 0v92.7l28.3-28.3a8 8 0 0 1 11.4 11.3l-42 42a8.2 8.2 0 0 1-11.4 0ZM216 144a8 8 0 0 0-8 8v56H48v-56a8 8 0 0 0-16 0v56a16 16 0 0 0 16 16h160a16 16 0 0 0 16-16v-56a8 8 0 0 0-8-8Z">
                            </path>
                          </svg>
                        </button>
                      </pane>
                      <pane :min-size="isMinSizePane4" :max-size="isMaxSizePane4"
                        class=" group hover:bg-blue-500/20 relative " :size="isSizepane345">
                        <button @click="dlCanvas('4pane_img_5', 'canvas4')" class="group-hover:flex justify-center items-center hidden bg-white lg:p-2 md:p-2 p-1.5  rounded-bl-md absolute 
                      right-0">
                          <svg class="lg:w-5 lg:h-5 md:h-5 md:w-5 w-4 h-4 fill-[#2155CD]"
                            xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
                            aria-hidden="true" role="img" preserveAspectRatio="xMidYMid meet" viewBox="0 0 256 256">
                            <path
                              d="M80.3 115.7a8 8 0 0 1 11.4-11.3l28.3 28.3V40a8 8 0 0 1 16 0v92.7l28.3-28.3a8 8 0 0 1 11.4 11.3l-42 42a8.2 8.2 0 0 1-11.4 0ZM216 144a8 8 0 0 0-8 8v56H48v-56a8 8 0 0 0-16 0v56a16 16 0 0 0 16 16h160a16 16 0 0 0 16-16v-56a8 8 0 0 0-8-8Z">
                            </path>
                          </svg>
                        </button>
                      </pane>

                      <pane :min-size="isMinSizePane5" :max-size="isMaxSizePane5"
                        class=" group hover:bg-blue-500/20 relative " :size="isSizepane345">
                        <button @click="dlCanvas('4pane_img_6', 'canvas5')" class="group-hover:flex justify-center items-center hidden bg-white lg:p-2 md:p-2 p-1.5  rounded-bl-md absolute 
                        right-0">
                          <svg class="lg:w-5 lg:h-5 md:h-5 md:w-5 w-4 h-4 fill-[#2155CD]"
                            xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
                            aria-hidden="true" role="img" preserveAspectRatio="xMidYMid meet" viewBox="0 0 256 256">
                            <path
                              d="M80.3 115.7a8 8 0 0 1 11.4-11.3l28.3 28.3V40a8 8 0 0 1 16 0v92.7l28.3-28.3a8 8 0 0 1 11.4 11.3l-42 42a8.2 8.2 0 0 1-11.4 0ZM216 144a8 8 0 0 0-8 8v56H48v-56a8 8 0 0 0-16 0v56a16 16 0 0 0 16 16h160a16 16 0 0 0 16-16v-56a8 8 0 0 0-8-8Z">
                            </path>
                          </svg>
                        </button>
                      </pane>
                    </splitpanes>
                  </pane>
                </splitpanes>
              </template>

              <div
                class="relative flex flex-row gap-3 justify-center items-center lg:mt-[1.85rem] md:mt-[1.85rem] mt-4">
                <button :class="isCroped"
                  class=" inline-flex items-center justify-center font-semibold lg:px-6 lg:py-1.5 md:px-6 md:py-1.5 px-4 py-1.5 text-white text-sm rounded-md bg-[#0AA1DD]"
                  @click="
                  dlCanvas('4pane_img_0', 'canvas0'),
                  dlCanvas('4pane_img_1', 'canvas1'),
                  dlCanvas('4pane_img_2', 'canvas2'),
                  dlCanvas('4pane_img_3', 'canvas3'),
                  dlCanvas('4pane_img_4', 'canvas4'),
                  dlCanvas('4pane_img_5', 'canvas5')">
                  <svg class="w-5 h-5 mx-2" xmlns="http://www.w3.org/2000/svg"
                    xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" role="img"
                    preserveAspectRatio="xMidYMid meet" viewBox="0 0 256 256">
                    <path fill="#ffffff"
                      d="M238 136v64a14 14 0 0 1-14 14H32a14 14 0 0 1-14-14v-64a14 14 0 0 1 14-14h48a6 6 0 0 1 0 12H32a2 2 0 0 0-2 2v64a2 2 0 0 0 2 2h192a2 2 0 0 0 2-2v-64a2 2 0 0 0-2-2h-48a6 6 0 0 1 0-12h48a14 14 0 0 1 14 14Zm-114.2-3.8a5.8 5.8 0 0 0 8.4 0l48-48a5.9 5.9 0 0 0-8.4-8.4L134 113.5V24a6 6 0 0 0-12 0v89.5L84.2 75.8a5.9 5.9 0 0 0-8.4 8.4ZM198 168a10 10 0 1 0-10 10a10 10 0 0 0 10-10Z">
                    </path>
                  </svg>
                  Download All

                </button>
              </div>

            </div>


          </div>

          <div class="flex-row flex-nowrap hidden">
            <div class="flex-none">
              <div class="flex flex-col items-end">
                <canvas class="object-cover" :width="isWidthCanvas0" :height="isHeightCanvas0" ref="canvas0"></canvas>
                <canvas class="object-cover" ref="canvas1" :width="isWidthCanvas1" :height="isHeightCanvas1"></canvas>
                <canvas class=" object-cover" ref="canvas2" :width="isWidthCanvas2" :height="isHeightCanvas2"></canvas>
              </div>
            </div>
            <div class="flex-none">
              <div class="flex flex-col items-start">
                <canvas class="object-cover" ref="canvas3" :width="isWidthCanvas3" :height="isHeightCanvas3"></canvas>
                <canvas class="object-cover" ref="canvas4" :width="isWidthCanvas4" :height="isHeightCanvas4"></canvas>
                <canvas class="object-cover" ref="canvas5" :width="isWidthCanvas5" :height="isHeightCanvas5"></canvas>
              </div>
            </div>
          </div>
        </div>
      </div>
      <Teleport to="body">

        <transition enter-from-class="transform opacity-0" enter-active-class="duration-300 ease-out"
          enter-to-class="opacity-100" leave-from-class="opacity-100" leave-active-class="duration-300 ease-in"
          leave-to-class="transform opacity-0 ">
          <template v-if="modal">
            <div class="fixed inset-0 bg-gray-900 opacity-50" aria-hidden="true"></div>
          </template>
        </transition>

        <transition enter-from-class="transform opacity-0 scale-75" enter-active-class="duration-300 ease-out"
          enter-to-class="opacity-100 scale-100" leave-from-class="opacity-100 scale-100"
          leave-active-class="duration-300 ease-in" leave-to-class="transform opacity-0 scale-75">
          <Modal v-if="modal" class="lg:mt-0 md:mt-0 mt-12">
            <template #modalPane>
              <div class="absolute right-0">
                <div @click="closeModal()" class="cursor-pointer hover:bg-red-100">
                  <svg class=" w-8 h-8" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
                    aria-hidden="true" role="img" preserveAspectRatio="xMidYMid meet" viewBox="0 0 24 24">
                    <path fill="#bb3535"
                      d="M6.4 19L5 17.6l5.6-5.6L5 6.4L6.4 5l5.6 5.6L17.6 5L19 6.4L13.4 12l5.6 5.6l-1.4 1.4l-5.6-5.6Z">
                    </path>
                  </svg>
                </div>
              </div>
              <OnClickOutside @trigger="closeModal()">
                <div class="px-4 pt-10 pb-4 h-auto lg:w-80 md:w-80 w-72">
                  <p class="text-xs mb-3 bg-[#E8F9FD] py-0.5 px-1 font-medium rounded text-center">
                    Choose and then start resizing the panel according to the panel you have chosen.
                  </p>
                  <div class="flex flex-row flex-wrap gap-3 justify-center items-center">
                    <div @click="this.$refs.pickpane0.click()" :class="pickPane.id == 0 ? 'bg-[#E8F9FD]' : 'bg-white'"
                      class="cursor-pointer grid relative grid-rows-2 grid-flow-col lg:gap-1 md:gap-1 gap-0.5 lg:h-20 md:w-20 lg:w-20 md:h-20 w-16 h-16 p-2 rounded-md hover:bg-[#E8F9FD]">
                      <input type="radio" ref="pickpane0" class="hidden" value="0" @change="changePane"
                        v-model="pickPane.id">
                      <div class="col-span-2 row-span-2 bg-[#0AA1DD] lg:p-4 md:p-4 p-2 rounded"></div>

                    </div>
                    <div @click="this.$refs.pickpane1.click()" :class="pickPane.id == 1 ? 'bg-[#E8F9FD]' : 'bg-white'"
                      class="cursor-pointer grid relative grid-rows-2 grid-flow-col lg:gap-1 md:gap-1 gap-0.5 lg:h-20 md:w-20 lg:w-20 md:h-20 w-16 h-16 p-2 rounded-md hover:bg-[#E8F9FD]">
                      <input type="radio" ref="pickpane1" class="hidden" value="1" @change="changePane"
                        v-model="pickPane.id">
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-4 md:p-4 p-2 rounded"></div>
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-4 md:p-4 p-2 rounded"></div>

                    </div>
                    <div @click="this.$refs.pickpane2.click()" :class="pickPane.id == 2 ? 'bg-[#E8F9FD]' : 'bg-white'"
                      class="cursor-pointer grid relative grid-rows-3 grid-flow-col gap-0.5 lg:h-20 md:w-20 lg:w-20 md:h-20 w-16 h-16 p-2 rounded-md hover:bg-[#E8F9FD]">
                      <input type="radio" ref="pickpane2" class="hidden" value="2" @change="changePane"
                        v-model="pickPane.id">
                      <div class="row-span-3 bg-[#0AA1DD] lg:p-4 md:p-4 p-2 rounded"></div>
                      <div class="row-span-3 bg-[#0AA1DD] lg:p-4 md:p-4 p-2 rounded"></div>

                    </div>
                    <div @click="this.$refs.pickpane3.click()" :class="pickPane.id == 3 ? 'bg-[#E8F9FD]' : 'bg-white'"
                      class="cursor-pointer grid relative grid-cols-3 grid-flow-row gap-0.5 lg:h-20 md:w-20 lg:w-20 md:h-20 w-16 h-16 p-2 rounded-md hover:bg-[#E8F9FD]">
                      <input type="radio" ref="pickpane3" class="hidden" value="3" @change="changePane"
                        v-model="pickPane.id">
                      <div class="col-span-3 row-span-1 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                      <div class="col-span-3 row-span-1 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                      <div class="col-span-3 row-span-1 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                    </div>
                    <!-- WIP -->
                    <!-- <div @click="this.$refs.pickpane4.click()" :class="pickPane.id == 4 ? 'bg-[#E8F9FD]' : 'bg-white'"
                      class="cursor-pointer grid relative grid-rows-1 grid-flow-col gap-0.5 lg:h-20 md:w-20 lg:w-20 md:h-20 w-16 h-16 p-2 rounded-md hover:bg-[#E8F9FD]">
                      <input type="radio" ref="pickpane4" class="hidden" value="4" @change="changePane"
                        v-model="pickPane.id">
                      <div class="row-span-3 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                      <div class="row-span-3 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                      <div class="row-span-3 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                    </div> -->
                    <div @click="this.$refs.pickpane5.click()" :class="pickPane.id == 5 ? 'bg-[#E8F9FD]' : 'bg-white'"
                      class="cursor-pointer grid relative grid-rows-2 grid-flow-col lg:gap-1 md:gap-1 gap-0.5 lg:h-20 md:w-20 lg:w-20 md:h-20 w-16 h-16 p-2 rounded-md hover:bg-[#E8F9FD]">
                      <input type="radio" ref="pickpane5" class="hidden" value="5" @change="changePane"
                        v-model="pickPane.id">
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-4 md:p-4 p-2.5 rounded"></div>
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-4 md:p-4 p-2.5 rounded"></div>
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-4 md:p-4 p-2.5 rounded"></div>
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-4 md:p-4 p-2.5 rounded"></div>

                    </div>
                    <div @click="this.$refs.pickpane6.click()" :class="pickPane.id == 6 ? 'bg-[#E8F9FD]' : 'bg-white'"
                      class="cursor-pointer grid relative grid-rows-2 grid-flow-col gap-0.5 lg:h-20 md:w-20 lg:w-20 md:h-20 w-16 h-16 p-2 rounded-md hover:bg-[#E8F9FD]">
                      <input type="radio" ref="pickpane6" class="hidden" value="6" @change="changePane"
                        v-model="pickPane.id">
                      <div class="row-span-2 bg-[#0AA1DD] lg:p-3 md:p-3 p-2.5 rounded"></div>
                      <div class="row-span-1 col-span-4 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                      <div class="row-span-1 col-span-4 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                    </div>

                    <div @click="this.$refs.pickpane7.click()" :class="pickPane.id == 7 ? 'bg-[#E8F9FD]' : 'bg-white'"
                      class="cursor-pointer grid grid-rows-3 grid-flow-col gap-0.5 lg:h-20 md:w-20 lg:w-20 md:h-20 w-16 h-16 p-2 rounded-md hover:bg-[#E8F9FD]">
                      <input ref="pickpane7" type="radio" class="hidden" value="7" @change="changePane"
                        v-model="pickPane.id">
                      <div class="row-span-3 bg-[#0AA1DD] lg:p-3 md:p-3 p-2.5 rounded"></div>
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-2 md:p-2 p-1.5 rounded"></div>
                    </div>
                    <div @click="this.$refs.pickpane8.click()" :class="pickPane.id == 8 ? 'bg-[#E8F9FD]' : 'bg-white'"
                      class="cursor-pointer grid relative grid-rows-2 grid-flow-col lg:gap-1 md:gap-1 gap-0.5 lg:h-20 md:w-20 lg:w-20 md:h-20 w-16 h-16 p-2 rounded-md hover:bg-[#E8F9FD]">
                      <input type="radio" ref="pickpane8" class="hidden" value="8" @change="changePane"
                        v-model="pickPane.id">
                      <div class="col-span-4 bg-[#0AA1DD] lg:p-4 md:p-4 p-2.5 rounded"></div>
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-4 md:p-4 p-2.5 rounded"></div>
                      <div class="col-span-2 bg-[#0AA1DD] lg:p-4 md:p-4 p-2.5 rounded"></div>
                    </div>
                  </div>
                </div>
              </OnClickOutside>
            </template>
          </Modal>
        </transition>
      </Teleport>
    </section>
  </main>
</template>

<style >
.splitpanes__splitter {
  background-color: #2155CD;
  position: relative;
}

.splitpanes__splitter:before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  transition: opacity 0.4s;
  background-color: rgba(0, 64, 255, 0.3);
  opacity: 0;
  z-index: 1;
}

.splitpanes__splitter:hover:before {
  opacity: 1;
}

.splitpanes--vertical>.splitpanes__splitter:before {
  left: -10px;
  right: -10px;
  height: 100%;
}

.splitpanes--horizontal>.splitpanes__splitter:before {
  top: -10px;
  bottom: -10px;
  width: 100%;
}
</style>