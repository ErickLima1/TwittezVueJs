<template>
    <v-dialog v-model="dialog" max-width="500px">
        <v-card>
            <v-card-title class="center-title">
                <v-chip variant="outlined">Login</v-chip>
            </v-card-title>
            <v-card-text>
                    <v-card-text>
                        <v-form ref="form">
                            <v-text-field v-model="email" label="Email" required type="email"
                                :rules="rules.email"></v-text-field>
                            <v-text-field v-model="password" label="Password" required type="password"
                                :rules="rules.password"></v-text-field>
                        </v-form>
                    </v-card-text>
                    <v-alert v-if="successMessage" type="success">{{ successMessage}}</v-alert>
                    <v-alert v-if="errorMessage" type="error">{{ errorMessage }}</v-alert>
                    <v-card-actions>
                        <v-btn color="primary" @click="authenticate">Login</v-btn>
                        <v-btn color="danger" @click="fecharLoginModal">Cancelar</v-btn>
                    </v-card-actions>
            </v-card-text>
        </v-card>
    </v-dialog>
</template>

<script>
import axios from "axios";
export default {
    data() {
        return {
            dialog: false,
            email: "",
            password: "",
            successMessage: "",
            errorMessage: "",

            rules: {
                email: [
                    (v) => !!v || "Email obrigatório",
                    (v) => /.+@.+\..+/.test(v) || "Email deve ser válido",
                ],
                password: [(v) => !!v || "Password obrigatório"],
            },
        };
    },
    methods: {
        abrirLoginModal() {
            this.dialog = true;
        },

        fecharLoginModal() {
            this.dialog = false, 
            this.email = "", 
            this.password = "";
            this.successMessage = '';
            this.errorMessage = '';
        },

        async authenticate() {
            if (!this.$refs.form.validate()) {
                return;
            }
            const userData = {
                email: this.email,
                password: this.password,
            };

            try {
                const response = await axios.post("http://127.0.0.1:3333/authenticate", userData);

                console.log("Token recebido:", response.data.token);
                // console.log(response)
                if (response.status === 200) {
                    this.successMessage = 'Usuario logado Com Sucesso.';
                    localStorage.setItem("token", response.data.token)// Armazenando o token no localStorage
                    this.$emit('authenticated', response.data.userName, response.data.token.token);
                    this.fecharLoginModal();
                }

            } catch (error) {
                if (error.response.status === 400 && error.response.data.message) {
                    this.errorMessage = error.response.data.message; 
                    // console.log("Erro 400:", error.response.data);
                    
                } else {
                    this.errorMessage = "Aconteceu Um Erro Inesperado.";
                    // console.log("Erro 500:", error.response.data);
                }
            }
        },
    },
};
</script>


<style>
.center-title {
    display: flex;
    justify-content: center;
    align-items: cemter;
}
</style>