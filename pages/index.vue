<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8>
      <v-card min-width="400">
        <v-snackbar
          v-model="snackbar"
          :timeout="4000"
          top
        >
          {{ message }}
          <v-btn color="pink" flat @click="snackbar = false">Close</v-btn>
        </v-snackbar>

        <v-card-title>
          <h2>Nuxt online chat</h2>
        </v-card-title>
        <v-card-text>
          <v-form ref="form" v-model="valid" lazy-validation>
            <v-text-field
              v-model="name"
              :counter="16"
              :rules="nameRules"
              label="Your nickname"
              required
            ></v-text-field>

            <v-text-field v-model="room" :rules="roomRules" label="Room chat" required></v-text-field>

            <v-btn :disabled="!valid" color="primary" @click="submit">Enter</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import { mapMutations } from "vuex";
export default {
  layout: "empty",
  head: {
    title: "Wellcome to chat"
  },
  sockets: {
    connect: function() {
      console.log("socket connected");
    }
  },
  data: () => ({
    valid: true,
    name: "",
    snackbar: false,
    message: '',
    nameRules: [
      v => !!v || "Enter your nickname",
      v => (v && v.length <= 16) || "Nickname must be less than 16 characters"
    ],
    room: "",
    roomRules: [v => !!v || "Enter your room"]
  }),
  mounted() {
    const {message} = this.$router.currentRoute.query
    if(message === 'noUser') {
      this.message = 'Not authorized'
    } else if(message === 'leftChat') {
      this.message = 'You left chat'
    }

    this.snackbar = !!this.message
  },
  methods: {
    ...mapMutations(["setUser"]),
    submit() {
      if (this.$refs.form.validate()) {
        const user = {
          name: this.name,
          room: this.room
        };

        this.$socket.emit("addUser", user, data => {
          if (typeof data === "string") {
            console.error(data);
          } else {
            user.id = data.userId;
            this.setUser(user);
            this.$router.push("/chat");
          }
        });
      }
    }
  }
};
</script>
