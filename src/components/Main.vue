<script>
import 'splitpanes/dist/splitpanes.css'
import { Splitpanes, Pane } from 'splitpanes'
import { Cropper } from 'vue-advanced-cropper'
import 'vue-advanced-cropper/dist/theme.compact.css';
import 'vue-advanced-cropper/dist/style.css';
export default {
  components: { Splitpanes, Pane, Cropper },
  data() {
    return {
      example: [
        {
          id: 1,
          url: 'img/example/1.jpg'
        },
        {
          id: 2,
          url: 'img/example/2.jpg'
        },
        {
          id: 3,
          url: 'img/example/3.jpg'
        }
      ],
      restart: false,
      percent: 100,
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
      canvas: {
        1: {
          w: 0,
          h: 0,
        },
        2: {
          w: 0,
          h: 0,
        },
        3: {
          w: 0,
          h: 0,
        },
        4: {
          w: 0,
          h: 0,
        }
      },
      pane: [
        {
          size: 50
        },
        {
          size: 50
        },
        {
          size: 50
        },
      ],
    }
  },
  methods: {
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
    setCanvas1() {

      this.resizeImage(this.cropedImg, this.coordinates.width, this.coordinates.height, 0, 0, (url) => {
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
      const yCanvas = this.coordinates.height / 2
      const checkYCanvas = this.restart ? this.canvas['1'].h : yCanvas
      const checkCoorW = this.restart ? this.canvas['1'].w : this.coordinates.width
      const checkCoorH = this.restart ? this.canvas['2'].h : this.coordinates.height

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
      const checkXCanvas = this.restart ? this.canvas['1'].w : xCanvas
      const checkCoorW = this.restart ? this.canvas['3'].w : this.coordinates.width
      const checkCoorH = this.restart ? this.canvas['3'].h : this.coordinates.height

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
      const yCanvas = this.coordinates.height / 2
      const xCanvas = this.coordinates.width / 2

      const checkXCanvas = this.restart ? this.canvas['1'].w : xCanvas
      const checkYCanvas = this.restart ? this.canvas['3'].h : yCanvas
      const checkCoorW = this.restart ? this.canvas['3'].w : this.coordinates.width
      const checkCoorH = this.restart ? this.canvas['4'].h : this.coordinates.height

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

    resizeCanvasY1() {
      this.restart = true
      this.resizeImage(this.cropedImg, this.canvas['1'].w, this.canvas['1'].h, 0, 0, (url) => {
        const canvas = this.$refs.canvas1;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });

      this.resizeImage(this.cropedImg, this.canvas['1'].w, this.canvas['2'].h, 0, this.canvas['1'].h, (url) => {
        const canvas = this.$refs.canvas2;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });

    },

    resizeCanvasY2() {
      this.restart = true
      this.resizeImage(this.cropedImg, this.canvas['3'].w, this.canvas['3'].h, this.canvas['1'].w, 0, (url) => {
        const canvas = this.$refs.canvas3;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });

      this.resizeImage(this.cropedImg, this.canvas['3'].w, this.canvas['4'].h, this.canvas['1'].w, this.canvas['3'].h, (url) => {
        const canvas = this.$refs.canvas4;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });
    },

    resizeCanvasX() {
      this.restart = true
      this.resizeImage(this.cropedImg, this.canvas['1'].w, this.canvas['1'].h, 0, 0, (url) => {
        const canvas = this.$refs.canvas1;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });


      this.resizeImage(this.cropedImg, this.canvas['1'].w, this.canvas['2'].h, 0, this.canvas['1'].h, (url) => {
        const canvas = this.$refs.canvas2;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });

      this.resizeImage(this.cropedImg, this.canvas['3'].w, this.canvas['3'].h, this.canvas['1'].w, 0, (url) => {
        const canvas = this.$refs.canvas3;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });

      this.resizeImage(this.cropedImg, this.canvas['3'].w, this.canvas['4'].h, this.canvas['1'].w, this.canvas['3'].h, (url) => {
        const canvas = this.$refs.canvas4;
        const ctx = canvas.getContext("2d");
        const imageObj = new Image();
        imageObj.src = url;
        imageObj.onload = function () {
          ctx.drawImage(imageObj, 0, 0);
        };
      });
    },
    reset() {
      this.image = {
        src: null,
        type: null
      }
    },
    loadImage(event) {
      const { files } = event.target;
      // Reference to the DOM input element
      // Ensure that you have a file before attempting to read it
      if (files && files[0]) {
        const fileType = files[0]['type'];
        const fileSize = files[0]['size'];
        const validImageTypes = ['image/gif', 'image/jpeg', 'image/png'];
        // TYPE IMAGE VALIDATION
        if (!validImageTypes.includes(fileType)) {
          alert('Please, choose the right format image! The avaiable format is JPG, JPEG, and PNG!');
        }
        // //SIZE IMAGE VALIDATION
        else if (fileSize >= 1045301) {
          alert('The maximum size image to upload is 1MB, please reduce your image size before upload again!');
        } else {
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

      this.setCanvas1();
      this.setCanvas2();
      this.setCanvas3();
      this.setCanvas4();
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
      return this.cropedImg ? '' : 'bg-[#E8F9FD]'
    }

  }
}
</script>
<template>

  <main class="w-full flex flex-wrap flex-col justify-center items-center">
    <section class="flex-1 mt-8 relative w-full">
      <div class="flex justify-center flex-wrap">
        <div class="relative flex-1 2xl:max-w-7xl lg:max-w-5xl md:max-w-3xl sm:max-w-xl max-w-max lg:mx-0 md:mx-0 mx-3">


          <div
            class="flex lg:gap-12 md:gap-12 sm:gap-14 gap-6 justify-center lg:flex-row md:flex-row flex-col flex-wrap items-center md:mx-3 mx-0">

            <div :class="isBg"
              class="order-1 lg:flex-1 md:flex-1 flex-none  rounded-xl 2xl:h-[25rem] 2xl:w-[20rem] lg:h-[20rem] lg:w-[18rem] md:w-72 md:h-72 w-full sm:h-64 h-52">


              <div
                class="flex h-full flex-col items-center justify-center lg:w-full md:w-full sm:w-full w-[15rem] mx-auto">
                <cropper  ref="cropper" :src="image.src" :stencil-props="{
                  aspectRatio: 1 / 1,
                }">
                </cropper>

              </div>
              <div
                class="lg:mt-3 md:mt-3 sm:mt-3 -mt-5 lg:text-base md:text-base sm:text-sm text-xs flex flex-row gap-3 lg:justify-end md:justify-end justify-center items-center">
                <button @click="getCrop" :class="isUploaded"
                  class="font-semibold lg:px-6 lg:py-1.5 md:px-6 md:py-1.5 px-4 py-1.5 text-white  rounded-md bg-[#0AA1DD]">
                  Crop
                </button>
                <button
                  class=" font-semibold lg:px-6 lg:py-1.5 md:px-6 md:py-1.5 px-4 py-1.5 text-white  rounded-md bg-[#0AA1DD]"
                  @click="$refs.file.click()">
                  <input class="hidden" type="file" ref="file" @change="loadImage($event)" accept="image/*">
                  Upload Image
                </button>

              </div>
            </div>

            <div
              class="lg:order-2 md:order-2 order-3 flex-none text-center lg:w-[18rem] lg:h-[18rem] md:w-64 md:h-64 w-60 h-60">
              <template v-if="this.cropedImg">
                <splitpanes
                  class="bg-cover bg-[#E8F9FD] border border-[#E8F9FD] lg:w-[18rem] lg:h-[18rem] md:w-64 md:h-64 w-60 h-60"
                  @resize="pane[2].size = $event[0].size, resizeCanvasX()" :style="{
                    backgroundImage: 'url(' + cropedImg + ')',
                    backgroundRepeat: 'no-repeat',
                  }">
                  <pane>
                    <splitpanes @resize="pane[0].size = $event[0].size, resizeCanvasY1()" horizontal>
                      <pane :size="pane[0].size" id="pane1" class=" group hover:bg-blue-500/20 relative">
                        <button @click="dlCanvas('4pane_img_1', 'canvas1')" class="justify-center group-hover:block hidden items-center bg-white lg:p-2 md:p-2 p-1.5  rounded-br-md absolute 
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
                      <pane class=" group hover:bg-blue-500/20 relative" :size="percent - pane[0].size" id="pane2">
                        <button @click="dlCanvas('4pane_img_2', 'canvas2')" class="group-hover:flex justify-center items-center hidden bg-white lg:p-2 md:p-2 p-1.5  rounded-br-md absolute 
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
                  <pane>
                    <splitpanes @resize="pane[1].size = $event[0].size, resizeCanvasY2()" horizontal>
                      <pane class=" group hover:bg-blue-500/20 relative" :size="pane[1].size" id="pane3">
                        <button @click="dlCanvas('4pane_img_3', 'canvas3')" class="group-hover:flex justify-center items-center hidden bg-white lg:p-2 md:p-2 p-1.5  rounded-bl-md absolute 
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
                      <pane class=" group hover:bg-blue-500/20 relative" :size="percent - pane[1].size" id="pane4">
                        <button @click="dlCanvas('4pane_img_4', 'canvas4')" class="group-hover:flex justify-center items-center hidden bg-white lg:p-2 md:p-2 p-1.5  rounded-bl-md absolute 
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

              <div class="relative flex flex-row gap-3 justify-center items-center lg:mt-4 md:mt-4 -mt-2">
                <button :class="isCroped"
                  class="absolute font-semibold -bottom-12 px-6 py-1.5 lg:text-base md:text-base sm:text-sm text-xs text-white  rounded-md bg-[#0AA1DD]"
                  @click="
                  dlCanvas('4pane_img_1', 'canvas1'),
                  dlCanvas('4pane_img_2', 'canvas2'),
                  dlCanvas('4pane_img_3', 'canvas3'),
                  dlCanvas('4pane_img_4', 'canvas4')">
                  Download All
                </button>
              </div>

            </div>
            <div class="lg:order-3 md:order-3 order-2 flex-1 basis-full">

              <span class="lg:text-sm md:text-sm text-xs font-semibold">Try Example~</span>
              <div class="flex flex-row flex-nowrap items-center space-x-6 mt-1">
                <div v-for="ex in example" :key="ex.id"
                  class=" font-semibold border-dashed border-2 border-[#2155CD] bg-cover cursor-pointer bg-center lg:w-16 lg:h-16 md:w-16 md:h-16 w-12 h-12 text-xs text-white  rounded-md"
                  :style="{
                    backgroundImage: 'url(' + ex.url + ')',
                    backgroundRepeat: 'no-repeat',
                  }" @click="image.src = `${getLocation()}${ex.url}`">

                </div>

              </div>

            </div>

          </div>

          <div class="flex-row flex-nowrap hidden">
            <div class="flex-none">
              <div class="flex flex-col items-end">
                <canvas class="object-cover" :width="canvas['1'].w = coordinates.width * pane[2].size / percent"
                  :height="canvas['1'].h = coordinates.height * pane[0].size / percent" ref="canvas1"></canvas>
                <canvas class=" object-cover" ref="canvas2"
                  :width="canvas['2'].w = coordinates.width * pane[2].size / percent"
                  :height="canvas['2'].h = coordinates.height * (percent - pane[0].size) / percent"></canvas>
              </div>
            </div>
            <div class="flex-none">
              <div class="flex flex-col items-start">
                <canvas class="object-cover" ref="canvas3"
                  :width="canvas['3'].w = coordinates.width * (percent - pane[2].size) / percent"
                  :height="canvas['3'].h = coordinates.height * pane[1].size / percent"></canvas>
                <canvas class="object-cover" ref="canvas4"
                  :width="canvas['4'].w = coordinates.width * (percent - pane[2].size) / percent"
                  :height="canvas['4'].h = coordinates.height * (percent - pane[1].size) / percent"></canvas>
              </div>
            </div>
          </div>
        </div>
      </div>
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
