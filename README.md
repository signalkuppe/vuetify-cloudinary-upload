# Vuetify Cloudinary Upload Widget 

A simple upload widget with progress indicator made for [Vuetify](https://vuetifyjs.com/) and [Cloudinary](https://cloudinary.com)

[Demo](https://i.imgur.com/3iTZ2in.gifv)

## Install

```bash
npm install vuetify-cloudinary-upload
```

## Usage

Import the component

```js
  <script>
    import vuetifyCloudinaryUpload from 'vuetify-cloudinary-upload'
    export default {
      components: { vuetifyCloudinaryUpload }
    }
  </script>
```

Use it in your template

```html
  <v-cloudinary-upload 
    v-model="image"
    upload-preset="cloudinary-preset-name"
    cloud-name="cloudinary-cloud-name"
  />
```

## Params

### v-model
Type: `String`
	
Required: the data you want to bind

### upload-preset
Type: `String`

Required: [The cloudinary upload preset](https://cloudinary.com/documentation/upload_images#upload_presets)

### cloud-name
Type: `String`

Required: The name of your Cloudinary account

### transformation
Type: `String`
Default: `w_120,h_120,c_fill,g_auto,q_auto,f_auto`

The image transformation (uploaded image format)

### button-color
Type: `String`
Default: ``

Vuetify's upload button color

### button-dark
Type: `Boolean`
Default: `false`

Upload button dark variant (makes text white)

### button-icon
Type: `String`
Default: `add_a_photo`

The upload button icon

### button-text
Type: `String`
Default: `Add image`

The upload button text

### delete-text
Type: `String`
Default: `Delete`

The delete button text

### progress-size
Type: `Number`
Default: `100`

Size of the circular progress indicator

### progress-stroke
Type: `Number`
Default: `15`

Stroke size of the circular progress indicator

### progress-color
Type: `String`
Default: `primary`

## Notes

The component uses FormData and XMLHttpRequest features, so watch out for browser support