<script>
import axios from 'axios';

export default {
  data() {
    return {
      posts: [],
      users: [],
      filteredPosts: [],
      searchTerm: '',
    };
  },

  beforeMount() {
    this.getData()   
  },

  methods: {
    getData() {
      let endpoints = [
        'https://jsonplaceholder.typicode.com/users',
        'https://jsonplaceholder.typicode.com/posts'        
      ]; 
      Promise.all(endpoints.map((endpoint) => axios.get(endpoint)))
        .then(([{ data: users }, { data: posts }]) => {
          this.users = users
          this.posts = posts
          this.addAuthorToPosts()
          this.filteredPosts = this.posts      
        });
    },      
    filterByAuthor() {
      const searchTerm = this.searchTerm.toLowerCase();
      this.filteredPosts = this.posts.filter(
        post => post.author?.toLowerCase().includes(searchTerm)
      );
    },
    addAuthorToPosts() {
      if (this.posts.length > 0 && this.users.length > 0) {
        this.posts = this.posts.map(post => {
          post.author = this.users.find(user => user.id === post.userId).name || "anonim"
          return post
        });        
      }
    },
    ucFirst(str) {
      if (!str) return str;
      return str[0].toUpperCase() + str.slice(1);
    }
  }
}
</script>

<template>
  <section class="album py-5 bg-light container-fluid min-vh-100">
    <h3 class="text-center">Posts</h3>
    <div class="input-group input-group-sm mb-3 container px-5">
      <div class="col-sm"></div>
      <div class="input-group-prepend">
        <span class="input-group-text" id="inputGroup-sizing-sm">🔍</span>
      </div>
      <input type="text" 
        class="form-control" 
        placeholder="Filter by author..." 
        aria-label="Small" 
        aria-describedby="inputGroup-sizing-sm"
        v-model="searchTerm"
        @input="filterByAuthor">
      <div class="col-sm"></div>
    </div>    
    
    <div v-if="filteredPosts.length > 0" class="container">
      <div class="card-columns">
        <div v-for="post in filteredPosts" :key="post.id" class="card p-3">            
          <h5 class="card-title text-primary">{{ ucFirst(post.title) }}</h5>            
          <p class="card-text">              
            <span>{{ ucFirst(post.body) }}</span>
          </p>            
          <p class="card-text">
            <small class="text-muted">{{ post.author }}</small>          
          </p>           
        </div>
      </div>
    </div>
    <div v-else-if="searchTerm" class="container">
      <h3 class="text-center">Nothing was found for the query "{{searchTerm}}"</h3>
    </div>
    <div v-else class="d-flex justify-content-center">
      <div class="spinner-border" role="status">
        <span class="sr-only">Loading...</span>
      </div>
    </div>
  </section>
</template>
  
<style scoped>
</style>