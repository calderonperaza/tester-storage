<template>
  <v-container>
    <v-row class="text-center">
      
    <v-col cols="4" offset="2">
      <v-file-input
          accept="image/*" 
          v-model="archivo"
          label="File input"
        ></v-file-input>                
    </v-col>
    <v-col cols="1">
    <v-btn @click="subirArchivo()" :loading="cargando" 
        :disabled="archivo==null" class="d-inline">Subir</v-btn>

    </v-col>      
    </v-row>
  <v-row>
    <v-col v-for="(item,index) in imagenes" :key="index" cols="2"
    class="d-flex child-flex elevation-5">
      <v-img :src="item"></v-img>
    </v-col>
  </v-row>


  </v-container>
</template>

<script>
import { storage } from "../firebase"


  export default {
    name: 'HelloWorld',

    data: () => ({
      archivo:null,
      cargando:false,
      imagenes:[],
    }),
    methods: {
      subirArchivo(){
        if (this.archivo){

          const ref = storage.ref()
          const carpeta="IDanuncio"           
          const archivoRef = ref.child(carpeta + "/" + this.archivo.name)
          this.cargando=true
          var this2=this
          
          archivoRef.put(this.archivo)
          .then(function(snapshot){
            console.log('Archivo subido')
            this2.cargando=false
            this2.archivo=null
            this2.leerImagenes()
          }).catch(function(error){
            console.log(error)
          })

        }

      },
    leerImagenes(){
      this.imagenes=[]
      const ref = storage.ref()
      const carpeta="IDanuncio"
      var this2=this
      ref.child(carpeta + "/").listAll()
            .then(function (result) {
                result.items.forEach(function (imgRef) {
                        //console.log(imgRef.toString());
                        imgRef.getDownloadURL().then(function (url) {
                            // console.log(url)                            
                            this2.imagenes.push(url);
                        })          
                    })                    
            });        
    }
    },
    created(){
      this.leerImagenes()
    }
  }
</script>
