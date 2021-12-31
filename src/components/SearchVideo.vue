<template>
  <div class="container">
    <!-- <input
        type="text"
        v-model="search"
        placeholder="Buscar videos"
    /> -->
    <div class="input-group mb-3">
      <input type="text" class="form-control" placeholder="Ingrese video a buscar" aria-label="Recipient's username" v-model="search" aria-describedby="button-addon2">
      <button class="btn btn-outline-secondary" type="button" id="button-addon2" v-on:click="getData(search)">Buscar</button>
    </div>
    <div v-if="posts && posts.length">
      <div v-for="post in posts" v-bind:key="post.id">
        <div class="card mb-3" style="max-width: 100%;">
          <div class="row g-0">
            <div class="col-md-4">
              <img v-bind:src="post.thumbnails.default.url" class="card-img-top" v-bind:alt="post.title">
            </div>
            <div class="col-md-8">
              <div class="card-body">
                <h5 class="card-title">{{ post.title }}</h5>
                <p class="card-text">{{ post.description.substring(0,300)+"..." }}</p>
                <p class="card-text"><small class="text-muted">{{ post.user }}</small></p>
              </div>
            </div>
          </div>
        </div> 
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      posts: [],
      search: "",
      uri: "https://emulator-search-backend.herokuapp.com"
      // uri: "http://localhost:8080"
    };
  },
  methods: {
    async login(){
      try {
        const response = await this.$http.post(
            this.uri + "/autenticar", { usuario: "asfo", contrasena: "holamundo" }
        );
        this.token = response.data.token;
        // if(this.search!=''){
        //   this.getData(this.search);
        // }else{
        //   this.getData('');
        // }
        this.getData('');
        // console.log('Autenticar', this.token);
      } catch (error) {
        console.log(error);
      }
    },
    async getData(search) {
      console.log(search);
      if(search!='' || search!=undefined){
        this.uriSearch = this.uri + "/videos?busqueda="+search;
      }else{
        this.uriSearch = this.uri + "/videos";
      }
      this.posts = [];
      try {
        const response = await this.$http.get(
            this.uriSearch, { 'headers': { 'access-token': this.token } }
        );
        this.videos = response.data;
        
        this.videos.forEach((item)=> {
          // console.log(item.snippet);
          this.posts.push(item.snippet);
        })
        console.log("Token", this.posts);
      } catch (error) {
        console.log(error);
        console.log("Token", this.token);
      }
    },
    // async getDataSearch(search) {
    //   try {
    //     const response = await this.$http.get(
    //         this.uri + "/videos?busqueda="+search, { 'headers': { 'access-token': this.token } }
    //     );
    //     this.videos = response.data;
    //     this.videos.forEach((item)=> {
    //       // console.log(item.snippet);
    //       this.posts.push(item.snippet);
    //     })
    //   } catch (error) {
    //     console.log(error);
    //     console.log("Token", token);
    //   }
    // }
    
      
  },
  computed: {
    // filteredList(){
		// 	this.getDataSearch(this.token, this.search);
		// }
  },
  created() {
    this.login();
  },
};
</script>