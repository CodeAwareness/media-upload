<template>
  <div>

    <div class="">
      <div class="gallery width-100" :class="error?'red-border':''">
        <Loader 
          color="#0275d8" 
          :active="loading" 
          spinner="line-scale" 
          background-color = 'rgba(255, 255, 255, .4)'
          />
        <div class="elements-wraper">

          <!--UPLOAD BUTTON-->
          <div class="button-container image-margin">
            <label for="images-upload" class="images-upload">
              <svg
                class="custum-icon"
                xmlns="http://www.w3.org/2000/svg" 
                width="1em" 
                height="1em" 
                preserveAspectRatio="xMidYMid meet" 
                viewBox="0 0 24 24">
                <g fill="none">
                <path 
                  d="M12 1C5.925 1 1 5.925 1 12s4.925 11 11 11s11-4.925 11-11S18.075 1 12 1zm1 15a1 1 0 1 1-2 0v-3H8a1 1 0 1 1 0-2h3V8a1 1 0 1 1 2 0v3h3a1 1 0 1 1 0 2h-3v3z" 
                  fill="currentColor"/>
                </g>
              </svg>
            </label>     
            <input @change="fileChange" id="images-upload" type="file" accept="image/*" multiple hidden>
          </div> 

          <!--IMAGES PREVIEW-->
          <div v-for="(image, index) in media" :key="index" class="image-container image-margin">
            <img :src="image.blob" alt=""  class="images-preview">
            <button @click="remove(index)" class="close-btn" type="button">
              <svg 
                class='times-icon'
                xmlns="http://www.w3.org/2000/svg" 
                width="1em" 
                height="1em" 
                preserveAspectRatio="xMidYMid meet" 
                viewBox="0 0 20 20"
                >
                <g fill="none">
                <path 
                  d="M7.172 14.243a1 1 0 1 1-1.415-1.415l7.071-7.07a1 1 0 0 1 1.415 1.414l-7.071 7.07z" 
                  fill="currentColor"
                  />
                <path 
                  d="M5.757 7.172a1 1 0 1 1 1.415-1.415l7.07 7.071a1 1 0 0 1-1.414 1.415l-7.07-7.071z" 
                  fill="currentColor"
                  />
                </g>
              </svg>
            </button>
          </div>
        </div>
      </div>
      <div v-if='error' id="media-required">
        <p class='red-text'>{{error}}</p>
      </div>
      <div v-for="(image, index) in media" :key="index" class="m-top">
        <input type="text" name="media[]" :value="image.name" hidden>
      </div>

    </div>
  </div>
</template>

<script>
import Loader from './loader/index.vue'
export default {
  data() {
    return {
      media: [],
      loading: false,
    }
  },
  props: {
    error:'',
    server: '',
    uploadFn: {
      type: Function,
    },
  },
  components:{Loader},
  methods: {
    async fileChange(event){
      this.loading=true
      let files = event.target.files
      for(var i=0; i < files.length; i++){
        let blob = URL.createObjectURL(files[i])
        const url = (typeof this.server === 'function')
          ? await this.server(files[i].name)
          : `${this.server}/${files[i].name}`
        const data = await this.uploadFn(url, files[i], { 'Content-Type': files[i].type })

        this.media.push({ blob, url, name: data.name, size: files[i].size, type: files[i].type});
      }
      this.loading=false
      this.media_emit()
    },
    remove(index) {
      this.media.splice(index,1)
      this.media_emit()
    },
    media_emit() {
      this.$emit('media',this.media)
    }
  },
  mounted() {
    this.$emit('media',this.media)
  },
}
</script>

<style scoped>
.image-wraper{
  min-height: 200px !important;
}

.gallery{
  background-color: #fbfbfb !important;
  border-radius: 5px !important;
  border-style: solid !important;
  border: 1px solid #bbbbbb !important;
  height: 85px !important;
  line-height: 1 !important;
  box-sizing: border-box !important;
  height: auto !important;
}

.images-upload {
  background-color: #ffffff !important;
  border-radius: 5px !important;
  border: 1px dashed #ccc !important;
  display: inline-block !important;
  cursor: pointer !important;
  width: 165px !important;
  height: 88px !important;
}
.images-upload:hover{
  background-color: #f1f1f1 !important;
}
.image-container{
  display: inline-table !important;
  height: 90px !important;
  display: flex !important;
}
.images-preview {
  border-radius: 5px !important;
  border: 1px solid #ccc !important;
  display: inline-block !important;
  width: auto;
  height: 88px !important;
  padding-top: -14px !important;
  transition: filter 0.1s linear;

}
.images-preview:hover{
  filter: brightness(90%);
}

.button-container{
  display: inline-flex !important;
  height: 90px !important;
  width: 140px !important;
  margin-right: 0.25rem !important;
  margin-left: 0.25rem !important;
}
.close-btn{
  background: none !important;
  color: white !important;
  border: none !important;
  padding: 0px !important;
  margin:0px !important;
  font: inherit !important;
  cursor: pointer !important;
  outline: inherit !important;
  position: relative !important;
  left: 0;
  top: -25px;
  box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px !important;
  width: 0px !important;

}
.times-icon{
  font-size: 3rem !important;
  padding: 0px !important;
  margin:0px !important;
}
.custum-icon{
  color: #00afca !important;
  font-size: 3rem !important;
  margin-top: 18px !important;
  margin-left: 44px !important;

}
.custum-icon:hover{
  color: #29818f !important;
}
.close-btn:hover{
  color: red !important;
  box-shadow: red 0px 7px 29px 0px !important;
}


/* -------------------- */


.width-100 {
  width: 100% !important;
}
.red-border {
  border: 1px solid #dc3545 !important;
  border-color: #dc3545 !important;
}

.elements-wraper {
  padding: 1rem !important;
  display: flex !important;
  flex-wrap: wrap !important;

}
.align-center {
  text-align: center !important;
}
.m-top-1 {
  margin-top: 0.25rem !important;
}

.image-margin {
  margin-right: 0.25rem !important;
  margin-left: 0.25rem !important;
  margin-top: 0.25rem !important;
  margin-bottom: 0.25rem !important;
}
.red-text {
  color: #d82335;
}

</style>




