<template>
  <div>
    <div class="tweet" v-if="!showUserTweets">
      <h2 class="mb-3 text-center my-5">Tweetz do Usuário</h2>
      <div class="mb-4" v-for="tweet in paginaUserTweets" :key="tweet.id">
        <hr>
        <div class="d-flex align-center">
          <v-avatar>
            <v-img cover src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSIJdSeJX1J_9Thml7ESAWIuJiV4jYwvs3lzjOHxlo50pMcbe7-PeNngi1WQh7HDAwTtZg&usqp=CAU"></v-img>
          </v-avatar>
          <div class="tweet ml-3">
            <h3>{{ tweet.user.username }}</h3>
            <p>{{ tweet.content }}</p>
            <span style="color: #888; font-size: 12px;">{{ tweet.created_at }}</span>
          </div>
        </div>
      </div>
    </div>
    <v-pagination v-model="currentPage" 
      :length="Math.ceil(userTweets.length / tweetsPerPage)" @input="currentPage = $event">
    </v-pagination>
  </div>
</template>


<script>
  import axios from "axios";
  
  export default {
    data() {
      return {
        userTweets: [],
        tweetsPerPage: 5,
        currentPage: 1,
      };
    },
    mounted() {
      this.fetchUserTweets();
    },
    computed: {
      paginaUserTweets() {
        const startTweets = (this.currentPage - 1) * this.tweetsPerPage;
        const endTweets = startTweets + this.tweetsPerPage;

        return this.userTweets.slice(startTweets, endTweets);
      },
    },
    methods: {
      async fetchUserTweets() {
        try {
          const token = localStorage.getItem('token');
          console.log('Token Do UserTweet:' ,token);
          const response = await axios.get("http://127.0.0.1:3333/tweets", {
            headers: {
              Authorization: `Bearer ${token}`,
            },
          });
          this.userTweets = response.data;
          console.log(this.userTweets);
        } catch (error) {
          console.error("Erro ao buscar os tweets:", error.response);
        }
      },
    },
  };
</script> 
  