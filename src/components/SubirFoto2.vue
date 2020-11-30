<template>
    <div>
           
        <v-file-input
            v-if="!uploadEnd && !uploading"
            v-model="file"
            accept="image/*"
            label="Seleccionar imagen a subir"
        ></v-file-input>
        
        
        <v-btn 
            :disabled="file==null"
            @click="upload()"
            color="primary"
            v-if="!uploadEnd && !uploading"            
        >Subir</v-btn>

      <v-progress-circular
        v-if="uploading && !uploadEnd"
        :size="100"
        :width="15"
        :rotate="360"
        :value="progressUpload"
        color="primary">
        {{ progressUpload }}
        %
      </v-progress-circular>


      <img
        v-if="uploadEnd"
        :src="downloadURL"
        width="400" />
      <div v-if="uploadEnd">
            <v-btn
            class="ma-0"
            dark
            small
            color="error"
            @click="deleteImage()"
            >
            Delete
            </v-btn>
      </div>
    </div>
</template>

<script>
import { storage } from "../firebase"

    export default {
     data () {
        return {
        progressUpload: 0,
        file: null,
        fileName:'',
        uploadTask: '',
        uploading: false,
        uploadEnd: false,
        downloadURL: ''
        }
    },
    methods: {
        upload () {        
        this.fileName = this.file.name
        this.uploading = true
        this.uploadTask = storage.ref('IDanuncio/' + this.file.name).put(this.file)
        },
    deleteImage () {
      storage
        .ref('IDanuncio/' + this.fileName)
        .delete()
        .then(() => {
          this.uploading = false
          this.uploadEnd = false
          this.downloadURL = ''
          console.log("la imagen se eliminó")
        })
        .catch((error) => {
          console.error(`sucedio un error: ${error}`)
        })
      this.file=null
      this.fileName=''
    }
  },
    watch: {
        uploadTask: function () {
        this.uploadTask.on('state_changed', sp => {
            this.progressUpload = Math.floor(sp.bytesTransferred / sp.totalBytes * 100)
        },
        null,
        () => {
            this.uploadTask.snapshot.ref.getDownloadURL().then(downloadURL => {
            this.uploadEnd = true
            this.downloadURL = downloadURL
            console.log("la imagen se subió correctamente")
            })
        })
        }
    }   
}
</script>

