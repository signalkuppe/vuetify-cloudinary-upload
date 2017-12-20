<template>
  <div>
    <!-- upload button -->
    <v-btn 
      @click.native="selectFiles" 
      :color="buttonColor"
      :dark="buttonDark"
      v-if="!imgPublicId && !uploading">
        {{ buttonText }}
        <v-icon 
        right
        aria-hidden="true">
          {{ buttonIcon }}
        </v-icon>
    </v-btn>
    <!-- Input file -->
    <input 
      id="files"
      type="file"
      name="file"
      ref="uploadInput"
      accept="image/*"
      :multiple="false"
      @change="uploadFiles($event)" />
    <!-- Progress -->
    <v-progress-circular
      v-if="uploading"
      :size="progressSize"
      :width="progressStroke"
      :rotate="360"
      :value="progress"
      :color="progressColor">
      {{progress}}%
    </v-progress-circular>
    <!-- Uploaded image -->
    <img
      v-if="imgPublicId"
      :src="imgSrc" />
    <!-- Delete button -->
    <div v-if="imgPublicId">
      <v-btn 
        class="ma-0"
        dark 
        small 
        color="error" 
        @click="deleteImage()" 
        > {{ deleteText }}
      </v-btn>
    </div>
  </div>
</template>

<script>
/*
* Uploads an image to cloudinary showning a progess indicator
* The image is shown after the upload, with a delete button to remove it
*/
export default {
  props: {
    // v-model
    value: {
      type: String,
      required
    },
    // Cloudinary upload preset
    uploadPreset: {
      type: String,
      required: true
    },
    // Cloudinary cloud name
    cloudName: {
      type: String,
      required: true
    },
    // Image transformations
    transformation: {
      type: String,
      default: 'w_120,h_120,c_fill,g_auto,q_auto'
    },
    // Upload button color (default = gray)
    buttonColor: {
      type: String,
      default: ''
    },
    // Upload button dark variant (makes text white)
    buttonDark: {
      type: Boolean,
      default: false
    },
    // Upload button icon on the right
    buttonIcon: {
      type: String,
      default: 'add_a_photo'
    },
    // Upload button text
    buttonText: {
      type: String,
      default: 'Add image'
    },
    // Delete button text
    deleteText: {
      type: String,
      default: 'Delete'
    },
    // Size of the circular progress indicator
    progressSize: {
      type: Number,
      default: 100
    },
    // Stroke size of the circular progress indicator
    progressStroke: {
      type: Number,
      default: 15
    },
    // Color of the circular progress indicator
    progressColor: {
      type: String,
      default: 'primary'
    }
  },
  data () {
    return {
      uploading: null,
      progress: 0
    }
  },
  computed: {
    // Public id on cloudinary, computed from v-model (value)
    imgPublicId: {
      get: function () {
        return this.value
      },
      set: function (val) {
        return val
      }
    },
    // Full image src, with transformation
    imgSrc: {
      get: function () {
        return `https://res.cloudinary.com/${this.cloudName}/image/upload/${this.transformation}/${this.imgPublicId}`
      },
      set: function (val) {
        return val
      }
    },
    // The url to POST the image to, based on cloudname
    apiEndpoint () {
      return `https://api.cloudinary.com/v1_1/${this.cloudName}/upload`
    }
  },
  methods: {
    // Triggers the click on the input file
    selectFiles () {
      this.$refs.uploadInput.click()
    },
    // Upload the image
    uploadFiles (e) {
      this.uploading = true
      let files = e.target.files || e.dataTransfer.files
      for (let i = 0; i < files.length; i++) {
        // Prepare the FormData to send to cloudinary
        const file = files.item(i)
        let formdata = new FormData()
        formdata.append('file', file)
        formdata.append('upload_preset', this.uploadPreset)
        let xhr = new XMLHttpRequest()
        // Progress
        xhr.upload.addEventListener('progress', (e) => {
          let total = e.total
          let loaded = e.loaded
          this.progress = parseInt((100 / total) * loaded)
        }, false)
        // Upload has finished
        xhr.addEventListener('load', (e) => {
          let response = e.target
          this.uploading = false
          this.progress = 0
          // reset formdata
          formdata = null
          if (response.status !== 200) { // success
            console.log('Image upload error', response.responseText)
            this.$emit('error', response.responseText)
          } else { // failure
            let uploaded = JSON.parse(response.responseText)
            this.imgPublicId = uploaded.public_id
            this.$emit('input', uploaded.public_id)
          }
        }, false)
        // Upload error
        xhr.addEventListener('error', (e) => {
          console.log('Image upload error', e)
          this.$emit('error', e)
        }, false)
        xhr.open('POST', this.apiEndpoint)
        xhr.send(formdata)
      }
    },
    deleteImage () {
      this.$emit('delete', this.imgPublicId)
      this.$emit('input')
      this.imgPublicId = null
    }
  }
}
</script>

<style scoped>

  /* visually hides the input file */

  input[type="file"] {
    position: absolute;
    clip: rect(0,0,0,0);
  }

</style>
