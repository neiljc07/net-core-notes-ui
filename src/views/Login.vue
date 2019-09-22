<template>
  <v-layout align-center justify-center>
    <v-flex xs12 sm8 md4>
      <v-card>
        <v-toolbar dark>
          <v-toolbar-title>Login Form</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <p>{{ message }}</p>
          <v-form
            ref="form"
            v-model="valid"
            lazy-validation>
            <v-text-field
              v-model="username"
              label="Username"
              :rules="usernameRules"
              required
            ></v-text-field>
            <v-text-field
              v-model="password"
              label="Password"
              :rules="passwordRules"
              type="password"
              required
            ></v-text-field>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn dark @click="submit">Login</v-btn>
          <v-btn dark to="/register">Register</v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>
</template>


<script>
  import axios from 'axios';
  import router from '../router';
  import { CONSTANTS } from '../config.js';

  export default {
    data: () => ({
      message: '',
      valid: true,
      username: '',
      usernameRules: [
        v => !!v || 'Username is required',
      ],
      password: '',
      passwordRules: [
        v => !!v || 'Password is required',
      ],
    }),

    methods: {
      submit () {
        const vm = this;

        if (this.$refs.form.validate()) {
          axios
            .post(CONSTANTS.API_URL + '/user/login', 
              { 
                username : vm.username, 
                password: vm.password 
              })
            .then(response => {
              vm.message = "Login Success";
              localStorage.setItem('user', JSON.stringify(response.data));
              router.push('/');
            })
            .catch(error => {
              if(error.response) {
                vm.message = error.response.data.message;
              }
            })
        }
      },
    },
  }
</script>
