<template>
  <v-row>
    <v-col cols="12" class="pr-3 pl-3 pt-0">
      <v-row no-gutters>
        <v-col 
          :lg="3"
          :md="3"
          :sm="6"
          :xs="12"
          v-for="item in notes"
          class="pr-2 pl-2 pb-4">
            <v-card
              class="mx-auto"
            >
              <v-card-text>
                <v-form
                  ref="form">
                  <v-textarea
                    v-model="item.note"
                    auto-grow
                    label="Note"
                    rows="3"
                  ></v-textarea>
                </v-form>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn 
                  dark 
                  small
                  @click="submit(item)">Save</v-btn>

                <v-btn 
                  small
                  dark
                  color="red"
                  @click="confirmDelete(item)">Delete</v-btn>
              </v-card-actions>
            </v-card>
        </v-col>
      </v-row>

      <v-btn
        dark
        bottom
        right
        fab
        fixed
        @click="addNote"
      >
        <v-icon>mdi-plus</v-icon>
      </v-btn>
    </v-col>

    <v-dialog
      v-model="dialog"
      max-width="290"
    >
      <v-card>
        <v-card-title class="headline">Are you sure you want to delete this note?</v-card-title>

        <v-card-actions>
          <div class="flex-grow-1"></div>
          <v-btn
            text
            @click="dialog = false"
          >
            Cancel
          </v-btn>

          <v-btn
            color="red"
            text
            @click="removeNote"
          >
            Delete
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
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
    notes : [],
    dialog : false,
    noteToBeRemoved : null
  }),

  beforeMount: function () {
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
  },

  methods: {
    submit(note) {
      axios
        .put(CONSTANTS.API_URL + '/note/' + note.id, 
          note, {
          headers: {
            Authorization: 'Bearer ' + JSON.parse(localStorage.getItem('user')).token
          }
        })
        .then(response => {
          console.log(response);
        })
        .catch(error => {
          console.log(error);
        })
    },

    addNote() {
      const user = JSON.parse(localStorage.getItem('user'));
      const vm = this;

      axios
        .post(CONSTANTS.API_URL + '/note', 
          {
            note: '',
            createdByID: user.id
          }, {
          headers: {
            Authorization: 'Bearer ' + user.token
          }
        })
        .then(response => {
          vm.notes.push(response.data);
        })
        .catch(error => {
          console.log(error);
        })
    },

    confirmDelete(note) {
      this.noteToBeRemoved = note;
      this.dialog = true;
    },

    removeNote() {
      this.dialog = false;
      const vm = this;
      
      axios
        .delete(CONSTANTS.API_URL + '/note/' + this.noteToBeRemoved.id, 
          {
            headers: {
              Authorization: 'Bearer ' + JSON.parse(localStorage.getItem('user')).token
            }
        })
        .then(response => {
          console.log(response);
          vm.notes.splice(vm.notes.indexOf(vm.noteToBeRemoved), 1);
        })
        .catch(error => {
          console.log(error);
        })
    }
  }
};
</script>
