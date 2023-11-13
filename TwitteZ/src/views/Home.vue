<template>
  <div class="vh-100 d-flex align-center justify-center">
    <div class="text-center">
      <v-textarea v-model="userMessage" class="twitter-textarea" label="O que você está pensando?" variant="outlined"></v-textarea>

      <v-card-actions class="d-flex justify-end align-center">
        <v-btn color="primary" @click="postUserTwitter">Publicar</v-btn>
      </v-card-actions>
      <hr>
    </div>

    <v-dialog v-model="avisoVisivel" max-width="400">
      <v-card>
        <v-card-text>
          <v-icon color="warning">mdi-alert</v-icon>
          <span class="ml-2">Para publicar, você precisa estar logado.</span>
        </v-card-text>
        <v-card-actions>
          <v-btn color="primary" @click="avisoVisivel = false">OK</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      userMessage: "",
      avisoVisivel: false,
    };
  },

  mounted() {
    this.postUserTwitter();
  },
  methods: {
    async postUserTwitter() {
      try {
        const token = localStorage.getItem("token");
        console.log("Token Do UserTweet:", token);

        if (!token) {
          console.log("Usuario Não Esta logado");
          this.avisoVisivel = true;

          setTimeout(() => {
            this.avisoVisivel = false;

          }, 5000);

          return;
        }

        const headers = {
          Authorization: `Bearer ${token}`,
        };

        const response = await axios.post("http://127.0.0.1:3333/tweets",
          { content: this.userMessage },
          { headers }
        );

        console.log('Resposta do servidor:', response.data);

        this.userMessage = '';
      } catch (error) {
        // console.log("Caiu no Catch", error); 
      }
    },
  },
};
</script>

<style>
.twitter-textarea {
  width: 500px;
  max-width: 100%;
  margin: 20px auto 0;
}
</style>
