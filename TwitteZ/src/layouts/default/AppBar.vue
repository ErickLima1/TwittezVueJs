<template>
  <div>
    <v-app-bar flat>

      <v-app-bar-title><v-icon icon="mdi-circle-slice-4" />TwitteZ</v-app-bar-title>
      <v-space></v-space>
      <router-link to="/"> <!----Aqui chat gpt-->
        <v-btn color="white">Inicio</v-btn>
      </router-link>

      <v-btn v-if="!isAuthenticated" @click="abrirModal">Cadastrar</v-btn>
      <v-btn v-if="!isAuthenticated" @click="abrirLoginModal">Entrar</v-btn>
      <v-btn v-else @click="logout">Sair</v-btn>

      <router-link to="/seus-twitters">
        <v-btn v-if="isAuthenticated" @click="showUserTweets = showUserTweets" color="white">Seus TwitteZ</v-btn>
      </router-link>

      <v-btn v-if="isAuthenticated" color="green" variant="outlined">{{ `Usuário: ${userName}` }}</v-btn>
    </v-app-bar>
    <CadastroModal ref="modal" @fechar="fecharModal"></CadastroModal>
    <LoginModal ref="loginModal" @fechar="fecharLoginModal" @authenticated="handleAuthenticated"></LoginModal>
    <UserTweets v-if="showUserTweets"  @authenticated="handleAuthenticated"></UserTweets>
    <TwittarModal v-if="twitterModal"></TwittarModal>
  </div>
</template>

<script>
import CadastroModal from "@/components/CadastroModal.vue";
import LoginModal from "@/components/LoginModal.vue";
import UserTweets from "@/views/UserTweets.vue";
// import TwittarModal from "@/components/TwittarModal.vue";

export default {
  data() {
    return {
      isAuthenticated: false,
      showUserTweets: false,
      // twitterModal: false,
      successMessage: "",
      userName: "",
    };
  },
  components: {
    CadastroModal,
    LoginModal,
    UserTweets,
    // TwittarModal,
  },
  methods: {
    handleAuthenticated(username, token) {
      this.isAuthenticated = true;
      this.userName = username;
      localStorage.setItem('token', token);
      localStorage.setItem("userName", username);
      console.log('Aqui o Token no AppBar: ',token);
      
    },
    
    logout() {
      localStorage.removeItem("token");
      this.isAuthenticated = false;
      this.successMessage = "";
    },

    abrirModal() {
      this.$refs.modal.abrirModal();
    },

    fecharModal() {
      this.$refs.modal.fecharModal();
    },

    abrirLoginModal() {
      this.$refs.loginModal.abrirLoginModal();
    },

    fecharLoginModal() {
      this.$refs.loginModal.fecharLoginModal();
    },

    refreshPage() {
      location.reload();
    },
  },
  mounted() {
    const token = localStorage.getItem("token");
    if (token) {
      this.isAuthenticated = true;
      
    }

    const storeUserName = localStorage.getItem("userName");

    if(storeUserName) {
      this.userName = storeUserName;
    }
  },
};
</script>
