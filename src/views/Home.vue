<template>
  <div class="home">
    <input v-model="search" class="searchBox" placeholder="Search"/>
    <Button @click.native="testFunction" label="Add Images"/>
    <div class="imageTileContainer">
      <ImageTile class="imageTile" v-for="image in images" :url="image.url" :name="image.name" v-bind:key="image.name" :labels="image.labels" :searchLabel="search"/>
    </div>
  </div>
  
</template>

<script>
// @ is an alias to /src
import Button from '@/components/Button.vue'
import ImageTile from '@/components/ImageTile.vue'
import firebase from "firebase"
export default {
  name: 'home',
  data(){
    return {
      images:[],
      search:""
    }
  },
  components: {
    Button,
    ImageTile
  },
  beforeMount(){
    let storageRef = firebase.storage().ref();
    let imageRef;
    let $ = this;
    firebase.firestore().collection("imageLabels").get()
    .then(imgData=>{
      imgData.forEach(image=>{
        imageRef = storageRef.child(image.data().name);
        imageRef.getDownloadURL().then(function(url) {
          $.images.push(image.data())
          $.images[$.images.length-1]["url"] = url
        })
      })
      console.log(this.images)
    })
  },
  methods: {
    testFunction : function(){
      console.log("STUFF")
      let storageRef = firebase.storage().ref();
      let files = document.getElementsByClassName("fileInput")[0].files
      if(files.length == 0){
        console.log("Select Images")
        return
      }
      console.log(files)
      Array.from(files).forEach(file=>{
        storageRef.child(file.name).put(file).then(function(snapshot) {
          console.log('Uploaded a blob or file!');
        });
      })
      
    }
  }
  
}
</script>
<style scoped>
.searchBox{
  padding: 30px;
  margin-top: -20px;
  font-size:130%;
  width: 70%;
}
.imageTileContainer{
  padding: 100px;
}
.imageTile{
  display: inline-block;
}

</style>