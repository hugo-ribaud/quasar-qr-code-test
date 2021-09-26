<template>
  <q-page class="flex flex-center">
    <div>
      <p class="error">{{ error }}</p>
      <p>{{ message }}</p>
        
       <q-btn color="blue-grey-10" rounded icon="camera_alt" label="Read QRCode"
          class="full-width" size="lg" @click="turnCameraOn()"
          v-show="!showCamera"/>

          <p class="text-subtitle1" v-if="result">Last result: <b>{{ result }}</b></p>
          <div v-if="showCamera">
      <qrcode-stream @decode="onDecode" @init="onInit" />
          </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
//importing QrcodeStream component from vue-qrcode-reader.
import { QrcodeStream } from "vue-qrcode-reader/src/index";
export default defineComponent({
  name: "PageIndex",
  components: { QrcodeStream },
  data() {
    return {
      result: "",
      error: "",
      message: "",
      showCamera: false
    };
  },
  methods: {
    //Creating onCode method to change result state to data receiving through scanner
    onDecode(result) {
      this.result = result;
    },
    turnCameraOn () {
      this.camera = 'auto'
      this.showCamera = true
    },
    turnCameraOff () {
      this.camera = 'off'
      this.showCamera = false
    },

    // cretaing onInit method to check for error or permission or browser not supported
    async onInit(promise) {
      try {
        await promise;
      } catch (error) {
        if (error.name === "NotAllowedError") {
          this.error = "ERROR: you need to grant camera access permisson";
        } else if (error.name === "NotFoundError") {
          this.error = "ERROR: no camera on this device";
        } else if (error.name === "NotSupportedError") {
          this.error = "ERROR: secure context required (HTTPS, localhost)";
        } else if (error.name === "NotReadableError") {
          this.error = "ERROR: is the camera already in use?";
        } else if (error.name === "OverconstrainedError") {
          this.error = "ERROR: installed cameras are not suitable";
        } else if (error.name === "StreamApiNotSupportedError") {
          this.error = "ERROR: Stream API is not supported in this browser";
        }
      }
    },
  },
});
</script>
