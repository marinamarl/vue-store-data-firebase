<template>
  <div id="app">
  <form class="vue-form" @submit.prevent="submit" v-on:submit.prevent="addUser" v-if="submitted == false">
    <div class="error-message">
      <p v-show="!newUser.email.valid">Please make sure the email address is valid. Example: someone@somewhere.com.</p>
    </div>

    <fieldset>
      <legend>Vue Contact Form</legend>
      <div>
        <label class="label" for="name">Name</label>
        <input type="text" name="name" id="name" required="" v-model="newUser.name">
      </div>

      <div>
        <label class="label" for="lastName">Last Name</label>
        <input type="text" name="lastName" id="lastName" required="" v-model="newUser.lastName">
      </div>

      <div>
        <label class="label" for="email">Email</label>
        <input type="email" name="email" id="email" required=""
               :class="{ email , error: !newUser.email.valid }"
               v-model="newUser.email.value">
      </div>
      <div>
        <label class="label" for="phone">Phone Number</label>
        <input type="number" name="phone" id="phone" min="0" oninput="validity.valid||(value='');"
         required="" v-model="newUser.phone.value">
      </div>
      <div>
        <label class="label" for="photo">Photo</label>
        <input type="text" name="photo" id="photo" required="" v-model="newUser.photo">
      </div>

      <div>
        <input type="submit" value="Send Form">
      </div>
    </fieldset>

  </form>

<div class="vue-form" v-else>
  <legend>Your contact details have been succesfully submitted. Thank you!</legend>
  <div>
    <input type="submit" value="Add another person!" onclick="location.reload(true)">
  </div>

</div>

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
        name: '',
        lastName: '',
        email: {
          value: '',
          valid: false
        },
        phone: '',
        Photo: ''
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
    }
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
