<template>
  <div id="app">
    <form class="vue-form" @submit.prevent="submit" v-if="submitted == false">
      <fieldset>
        <legend>Vue Contact Form</legend>
        <!-- It would be a good idea to have extra validation for each field (not all browsers behave the same with 'required'). -->
        <div>
          <label class="label" for="name">Name</label>
          <input type="text" name="name" id="name" required v-model="newUser.name">
        </div>
        <div>
          <label class="label" for="lastName">Last Name</label>
          <input type="text" name="lastName" id="lastName" required v-model="newUser.lastName">
        </div>
        <div>
          <label class="label" for="email">Email</label>
          <input type="email" name="email" id="email" required v-model="newUser.email">
        </div>
        <div>
          <label class="label" for="phone">Phone Number</label>
          <input type="number" name="phone" id="phone" min="0" required v-model="newUser.phone">
        </div>
        <div>
          <input type="file" name="file" id="file" required @change="onFileChanged">
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
  </div>
</template>

<script>
import firebase from 'firebase/app'
import 'firebase/database'
import 'firebase/storage'

let config = {
  apiKey: 'AIzaSyCMDAI881ToJhye3bPCJfjkgw9sc9AU4Ho',
  authDomain: 'contactformvue.firebaseapp.com',
  databaseURL: 'https://contactformvue.firebaseio.com',
  projectId: 'contactformvue',
  storageBucket: 'contactformvue.appspot.com',
  messagingSenderId: '86941929496'
}

let app = firebase.initializeApp(config)
let db = app.database()
let ref = app.storage().ref()
let usersRef = db.ref('users')

export default {
  name: 'App',
  firebase: {
    users: usersRef
  },
  data: function () {
    return {
      newUser: {
        name: '',
        lastName: '',
        email: '',
        phone: '',
        imgUrl: ''
      },
      selectedFile: null,
      submitted: false
    }
  },
  methods: {
    // submit form handler
    async submit () {
      try {
        let uploadFile = await ref.child(this.selectedFile.name).put(this.selectedFile)
        this.newUser.imgUrl = await uploadFile.ref.getDownloadURL()
      } catch (e) {
        // error uploading image
        console.error(e)
      }
      usersRef.push(this.newUser)
      this.submitted = true
    },

    // upload image name watcher
    onFileChanged (event) {
      this.selectedFile = event.target.files[0]
      this.newUser.photo = this.selectedFile.name
    }
  }
}

</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
  div {
    display: flex;
    justify-content: center;
    flex-direction: column;
    width: 50%;
    margin: auto;
  }
  fieldset {
    margin: auto;
  }
  fieldset, input {
    border: 1px solid gray;
    border-radius: 5px;
  }
  label, input {
    margin: 5px;
  }
  input{padding: 5px;}
</style>
