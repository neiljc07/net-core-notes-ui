<template>
  <v-layout align-center justify-center>
    <v-flex xs12 sm8 md4>
      <v-card>
        <v-toolbar dark>
          <v-toolbar-title>Edit Profile</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <p class="text-center" v-html="message"></p>
          <v-form
            ref="form"
            v-model="valid"
            lazy-validation>

            <v-text-field
              v-model="displayName"
              label="Display Name"
              disabled
            ></v-text-field>

            <v-text-field
              v-model="username"
              label="Username"
              disabled
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

          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn dark @click="submit">Save</v-btn>
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

      displayName: '',
      username: '',
      
      password: '',
      passwordRules: [
        v => !!v || 'Password is required',
        v => (v && v.length <= 30) || 'Password must not be greater than 30 characters',
        v => (v && v.length >= 6) || 'Password must be greater than or equal to 6 characters',
      ],

      confirmPassword: '',
    }),

    mounted: function() {
      this.displayName = JSON.parse(localStorage.getItem('user')).displayName;
      this.username = JSON.parse(localStorage.getItem('user')).username;
    },

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
        const user = JSON.parse(localStorage.getItem('user'));

        if (this.$refs.form.validate()) {
          axios
            .put(CONSTANTS.API_URL + '/user/UpdatePassword/' + user.id, 
              { 
                id : user.id,
                password: vm.password 
              },
              { 
                headers: {
                  Authorization: 'Bearer ' + user.token
                }
              })
            .then(response => {
              vm.message = "<span class='green--text'>Password Update Success</span>";
            })
            .catch(error => {
              vm.message = "";

              if(error.response) {
                if(error.response.data.code == '00009') {
                  error.response.data.errors.forEach((item) => {
                    vm.message += item.message[0].errorMessage + '<br>';
                  });
                } else {
                  vm.message = error.response.data.message;
                }
              }
            })
        }
      },
    },
  }
</script>
