<template>
    <v-dialog v-model="dialog" max-width="500px">
        <v-card>
            <v-card-title class="center-title">
                <v-chip variant="outlined">Cadastro</v-chip>
            </v-card-title>
            <v-card-text>
                <v-form ref="form">
                    <v-text-field v-model="username" label="Username" :rules="rules.username"></v-text-field>
                    <v-text-field v-model="email" label="Email" required type="email" 
                        :rules="rules.email"></v-text-field>
                    <v-text-field v-model="password" label="Password" required type="password" 
                        :rules="rules.password"></v-text-field>
                </v-form>
            </v-card-text>
            <v-alert v-if="successMessage" type="success">{{ successMessage }}</v-alert>
            <v-alert v-if="errorMessage" type="error">{{ errorMessage }}</v-alert>
            <v-card-actions>
                <v-btn color="primary"  @click="cadastrar">Cadastrar</v-btn>
                <v-btn color="danger" @click="fecharModal">Cancelar</v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
</template>

<script>
import axios from 'axios';

export default {
    data() {
        return {
            dialog: false,
            username: '',
            email: '',
            password: '',
            successMessage: '',
            errorMessage: '',

            rules: {
                username: [
                    v => !!v || 'Username obrigatório'
                ],
                email: [
                    v => !!v || 'Email obrigatório',
                    v => /.+@.+\..+/.test(v) || 'Email deve ser válido',
                ],
                password: [
                    v => !!v || 'Password obrigatório',
                ],
            },
        };
    },
    methods: {
        abrirModal() {
            this.dialog = true;
        },
        fecharModal() {
            this.dialog = false;
            this.username = '';
            this.email = '';
            this.password = '';
            this.successMessage = '';
            this.errorMessage = '';
        },
        async cadastrar() {
            if (!this.$refs.form.validate()) {
                return;
            }
            
            const userData = {
                username: this.username,
                email: this.email,
                password: this.password,
            };
            try {
                const response = await axios.post('http://127.0.0.1:3333/register', userData);
                if (response.status === 200 && response.data.message) {
                    this.successMessage = response.data.message;
                    setTimeout(() => {
                        this.successMessage = '';
                    }, 3000);
                } 
                
            } catch (error) {
                if (error.response.status === 400) {
                    // console.log('Erro 400:', error.response.data);
                    this.errorMessage = error.response.data.message[0].message;
                } else {
                   this.errorMessage = 'Erro Inesperado Aconteceu.'
                }
            }
        }
    }
}
</script>

<style>
    .center-title {
        display: flex;
        justify-content: center;
        align-items: cemter;
    }
</style>