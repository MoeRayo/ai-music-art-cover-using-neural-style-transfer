<template>
  <div class="bg-washed-green vh-100 ph3 ph5-ns">

    <header class="tc pv3">
      <h1 class="tl tc tl-ns mt2 mb0 calisto fw5 f2-ns f3 mid-gray">Music art covers using neural style transfer</h1>
    </header>

     <section class="flex flex-wrap justify-between">

      <div class="w-100 w-50-ns tc tl-ns br b--black-50">

        <h2 class="calisto fw1 f4-ns f3">Create image</h2>
        <h3 class="calisto fw1 f6 black-80">Select an image</h3>


        <div v-for="image in images" :key="image.id" class="fl-ns db tc pa1 grow aspect-ratio--4x6 mb3" :class="image.id === imageId ? 'ba bw2 b--purple ' : ''" @click="selectImage(image.id, image.publicId)">
          <cld-image :public-id="image.publicId" crop="scale" width="130" height="100"></cld-image>
        </div>

        <p v-if="error" class="cb red fw3 i f7">Please fill the required fields</p>
        
        <div class="mv4 cb">
          <div class="mb3">
            <label class="mb1 ttc f7 db b" for="image-text">Text</label>
            <input id="image-text" v-model="text" type="text" name="image-text" class="dib w-100 w-40-l w-70-m pv3 ph2 br2 ba b--black-40 f7 bg-washed-blue" required>
            <button class="f6 link dim br2 pv3 ph2 mb2 dib white bg-navy ba b--navy pointer mt3 mt0-l" @click="upload">Generate image</button>
          </div>

          <div>
            <h2 class="calisto fw1 f5 underline">Generated Image Result</h2>
            <div v-if="showImage" class="mt-10 dn">
              <GeneratedImageCover ref="ref"
                :text="text"
                :public-id="publicId"
              />
            </div>

            <p v-if="showProgress" class="black-80 tc i">image loading...</p>
            <section v-if="results && results.secure_url">
              <img :src="results.secure_url" :alt="results.public_id" class="mv3" />
            </section>
          </div>

        </div>
        
      </div>

      <div class="w-100 w-50-ns tc tl-ns pl4">
        <h2 class="calisto fw1 f4-ns f3 ">Apply neural style transfer</h2>
        <h3 class="calisto fw1 f6 black-80">Select an Album</h3>

        <div v-for="album in albums" :key="album.id" class="fl-ns db tc pa1 grow aspect-ratio--4x6 mb3" :class="album.id === albumId ? 'ba bw2 b--green ' : ''" @click="selectAlbum(album.id, album.publicId)">
          <cld-image :public-id="album.publicId" crop="scale" width="130" height="100"></cld-image>
        </div>

        <button class="cb f6 link dim br2 pv3 ph2 mb2 db white bg-navy ba b--navy pointer mt4" @click="applyStyle">Apply neural style transfer</button>
        <div v-if="showAlbum">
          <GeneratedAlbum 
            :albumId="publicId"
            :publicid="results.public_id"
          />
        </div>



      </div>
      
    </section>
    
  </div>
</template>

<script>
  import images from "~/utils/image-covers.json";
  import albums from "~/utils/album-covers.json";

  export default {
    data() {
      return {
        images,
        albums,
        imageId: null,
        albumId:null,
        cloudName: "thefolu",
        preset: "duxf364a",
        showImage: false,
        publicId: null,
        tags: "music-art-cover",
        fileContents: null,
        text: '',
        formData: null,
        showProgress: false,
        file: null,
        results: null,
        error: '',
        showAlbum: false,
        img: null,
        dataUrl: null
      }
    },
  
    methods: {
      selectImage(imageId, publicId) {
        this.imageId = imageId
        this.publicId = publicId
        this.error = false
        this.showImage = false
      },
      selectAlbum(albumId, publicId) {
        this.albumId = albumId
        this.publicId = publicId
      },
      applyStyle(){
        this.showAlbum = true 
      },
      prepareFormData() {
        this.formData = new FormData();
        this.formData.append("upload_preset", this.preset);
        this.formData.append("tags", this.tags); // Optional - add tag for image admin in Cloudinary
        this.formData.append("file", this.fileContents);
      },
      getBase64Image(url) {
        this.img = new Image();
        this.img.setAttribute('crossOrigin', 'anonymous');
        this.img.onload = () => {
          const canvas = document.createElement("canvas");
          canvas.width = this.img.width;
          canvas.height = this.img.height;
          const ctx = canvas.getContext("2d");
          ctx.drawImage(this.img, 0, 0);
           this.dataURL = canvas.toDataURL("image/png");
          this.fileContents = this.dataURL
        }
        this.img.src = url
      },
      upload () {
        if (!this.imageId || this.text === '') {
          this.error = true
        } else {
          this.showImage = true 
        } 
        this.$nextTick(() => {
          this.getBase64Image(this.$refs.ref.$refs.ref.$el.src)
        });
        setTimeout(() => {
          this.prepareFormData();
          const cloudinaryUploadURL = `https://api.cloudinary.com/v1_1/${this.cloudName}/upload`;
          const requestObj = {
            url: cloudinaryUploadURL,
            method: "POST",
            data: this.formData,
          };
          this.showProgress = true;
          this.$axios(requestObj)
          .then(response => {
            this.results = response.data;
          })
          .catch(error => {
            return error
          }).finally(() => {
            this.showProgress = false;
          })
        }, 3000);
      },
    }
  }
</script>

