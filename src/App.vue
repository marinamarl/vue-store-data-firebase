<template>
  <div id="app">
  <form class="vue-form" @submit.prevent="submit" v-on:submit.prevent="addUser">

    <div class="error-message">
      <p v-show="!newUser.email.valid">Oh, please enter a valid email address.</p>
    </div>

    <fieldset>
      <legend>Vue Contact Form</legend>
      <div>
        <label class="label" for="name">Name</label>
        <input type="text" name="name" id="name" required="" v-model="newUser.name">
      </div>
      <div>
        <label class="label" for="email">Email</label>
        <input type="email" name="email" id="email" required=""
               :class="{ email , error: !newUser.email.valid }"
               v-model="newUser.email.value">
      </div>
      <div>
        <input type="submit" value="Send Form">
      </div>
    </fieldset>
  </form>

  <div class="debug">
    <pre><code>{{ $data }}</code></pre>
  </div>
  </div>
</template>

<script>
import Firebase from 'firebase'
let config = {
  apiKey: 'AIzaSyCMDAI881ToJhye3bPCJfjkgw9sc9AU4Ho',
  authDomain: 'contactformvue.firebaseapp.com',
  databaseURL: 'https://contactformvue.firebaseio.com',
  projectId: 'contactformvue',
  storageBucket: 'contactformvue.appspot.com',
  messagingSenderId: '86941929496'
}

let app = Firebase.initializeApp(config)
let db = app.database()
let usersRef = db.ref('users')
let emailRegExp = /^[a-zA-Z0-9.!#$%&'*+\/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$/;

export default {
  name: 'App',
  firebase: {
    users: usersRef
  },
  data: function() {
    return {
      newUser: {
        name: "",
        email: {
          value: "",
          valid: false
        }
      },
      submitted: false
    };
  },
  methods: {
    // send data to firebase
    addUser: function () {
      usersRef.push(this.newUser);
      this.newUser.name = '';
      this.newUser.email.value = '';
    },

    // submit form handler
    submit: function() {
      this.submitted = true;
    },
    // validate by type and value
    validate: function(type, value) {
      if (type === "email") {
        this.newUser.email.valid = this.isEmail(value) ? true : false;
      }
    },
    // check for valid email adress
    isEmail: function(value) {
      return emailRegExp.test(value);
    },
  },
  watch: {
    // watching nested property
    "newUser.email.value": function(value) {
      this.validate("email", value);
    }
  }
}

</script>

<style>
  @import 'assets/contactForm.css';
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
