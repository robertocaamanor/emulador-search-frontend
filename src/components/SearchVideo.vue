<template>
  <div class="container">
    <!-- <input
        type="text"
        v-model="search"
        placeholder="Buscar videos"
    /> -->
    <div class="input-group mb-3">
      <span class="input-group-text" id="basic-addon1">Buscar video</span>
      <input type="text" class="form-control" aria-label="Username" aria-describedby="basic-addon1" v-model="search" placeholder="Buscar videos" />
    </div><br />
    <div v-if="filteredList && filteredList.length">
      <div v-for="post in filteredList" v-bind:key="post.id">
        <div class="card mb-3" style="max-width: 100%;">
          <div class="row g-0">
            <div class="col-md-4">
              <img v-bind:src="'https://emulator-search-backend.herokuapp.com/' + post.image" class="card-img-top" v-bind:alt="post.title">
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
    };
  },
  methods: {
    async login(){
      try {
        const response = await this.$http.post(
            this.uri + "/autenticar", { usuario: "asfo", contrasena: "holamundo" }
        );
        this.token = response.data.token;
        this.getData(this.token);
        console.log('Autenticar', this.token);
      } catch (error) {
        console.log(error);
      }
    },
    async getData(token) {
      try {
        const response = await this.$http.get(
            this.uri + "/videos", { 'headers': { 'access-token': token } }
        );
        this.posts = response.data;
        console.log("Token", token);
        console.log(this.posts);
      } catch (error) {
        console.log(error);
        console.log("Token", token);
      }
    }
    
      
  },
  computed: {
    filteredList(){
			return this.posts.filter(posts => {
				return posts.title.toLowerCase().indexOf(this.search.toLowerCase()) !== -1
			})
		}
  },
  created() {
    this.login();
  },
};
</script>