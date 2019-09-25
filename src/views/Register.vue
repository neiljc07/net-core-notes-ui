<template>
  <v-layout align-center justify-center>
    <v-flex xs12 sm8 md4>
      <v-form
        ref="form"
        v-model="valid"
        lazy-validation>

        <v-card>
          <v-toolbar dark>
            <v-toolbar-title>Registration Form</v-toolbar-title>
          </v-toolbar>
          <v-card-text>
            <p class="text-center" v-html="message"></p>
            
            <v-text-field
              v-model="displayName"
              label="Display Name"
              :rules="displayNameRules"
              required
            ></v-text-field>

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

            <v-text-field
              v-model="confirmPassword"
              label="Confirm Password"
              :rules="confirmPasswordRules"
              type="password"
              required
            ></v-text-field>

          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn dark @click="submit">Register</v-btn>
            <v-btn to="/login">Back to Login</v-btn>
          </v-card-actions>
        </v-card>
      </v-form>
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

      displayName: '',
      displayNameRules: [
        v => !! v || 'Display Name is required',
        v => (v && v.length <= 30) || 'Display Name must not be greater than 30 characters',
        v => (v && v.length >= 6) || 'Display Name must be greater than or equal to 6 characters',
      ],
      
      username: '',
      usernameRules: [
        v => !! v || 'Username is required',
        v => (v && v.length <= 20) || 'Username must not be greater than 20 characters',
        v => (v && v.length >= 6) || 'Username must be greater than or equal to 6 characters',
        v => /^[a-zA-Z0-9_]+$/.test(v) || 'Username must not contain special characters'
      ],
      
      password: '',
      passwordRules: [
        v => !!v || 'Password is required',
        v => (v && v.length <= 30) || 'Password must not be greater than 30 characters',
        v => (v && v.length >= 6) || 'Password must be greater than or equal to 6 characters',
      ],

      confirmPassword: '',
    }),

    computed: {
      confirmPasswordRules() {
        return [
          () => (this.confirmPassword === this.password) || 'Password must match'
        ];
      }
    },

    methods: {
      submit () {
        const vm = this;

        if (this.$refs.form.validate()) {
          axios
            .post(CONSTANTS.API_URL + '/user', 
              { 
                displayName : vm.displayName,
                username : vm.username, 
                password: vm.password 
              })
            .then(response => {
              vm.message = "Registration Success";
              localStorage.setItem('user', JSON.stringify(response.data));
              router.push('/');
            })
            .catch(error => {
              vm.message = "";

              if(error.response) {
                if(error.response.data.code == '00009') {
                  error.response.data.errors.forEach((item) => {
                    vm.message += "<span class='red--text'>" + item.message[0].errorMessage + "</span><br>";
                  });
                } else {
                  vm.message = "<span class='red--text'>" + error.response.data.message + "</span>";
                }
              }
            })
        }
      },
    },
  }
</script>
