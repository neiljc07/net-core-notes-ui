<template>
  <v-container fluid>
    <v-row
      class="mb-6"
      no-gutters
    >
      <v-col cols="2">
        <Sidebar />
      </v-col>

      <v-col cols="10">

        <v-row no-gutters>
          <v-col 
            cols="3"
            v-for="item in notes">
              <v-card
                class="mx-auto"
              >
                <v-card-text>{{ item.note }}</v-card-text>

                <v-card-actions>
                  <v-btn text>Click</v-btn>
                </v-card-actions>
              </v-card>
          </v-col>
        </v-row>

        
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios';
import Sidebar from '../components/Sidebar';
import { CONSTANTS } from '../config.js';

export default {
  components: {
    Sidebar,
  },
  data: () => ({
    notes : []
  }),

  mounted: function () {
    const self = this;


    axios
      .get(CONSTANTS.API_URL + '/note', {
        headers: {
          Authorization: 'Bearer ' + JSON.parse(localStorage.getItem('user')).token
        }
      })
      .then(response => {
        self.notes = response.data;
      })
      .catch(error => {

        // TODO REDIRECT TO LOGIN IF 401
        console.log(error);
      })
  }
};
</script>
