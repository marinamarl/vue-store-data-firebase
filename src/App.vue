<template>
  <div id="app">
  <form class="vue-form" @submit.prevent="submit" v-if="submitted == false">
    <div class="error-message">
      <p v-show="!newUser.email.valid">Please make sure the email address is valid. Example: someone@somewhere.com.</p>
    </div>

    <fieldset>
      <legend>Vue Contact Form</legend>
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
        <input type="email" name="email" id="email" required
               :class="{ error: !newUser.email.valid }"
               v-model="newUser.email.value">
      </div>
      <!-- @change kai oxi watcher -->
      <div>
        <label class="label" for="phone">Phone Number</label>
        <input type="number" name="phone" id="phone" min="0"
         required v-model="newUser.phone">
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
let ref = app.storage().ref()
let usersRef = db.ref('users')
let emailRegExp = /^[a-zA-Z0-9.!#$%&'*+=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$/
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
        email: {
          value: '',
          valid: false
        },
        phone: ''
      },
      selectedFile: null,
      submitted: false
    }
    // It would be a good idea to have and added validation for each field, and not only for email (not all browsers behave the same with 'required').
    // na pairneis kai to url ths fwto meso tou promise otan anebainei (prwta stelneis thn fwto kai meta ta data)
  },
  methods: {
    // submit form handler
    submit () {
      let file = this.selectedFile
      let name = file.name
      usersRef.push(this.newUser)
      ref.child(name).put(file)
      .then(snapshot => {
       return snapshot.ref.getDownloadURL();   // Will return a promise with the download link
   })
      .then(downloadURL => {
   console.log(`Successfully uploaded file and got download link - ${downloadURL}`);
   return downloadURL;
})
      // this.newUser.name = ''
      // this.newUser.email.value = ''
      // this.newUser.phone = ''
      // this.file.downloadURL= ''
      this.submitted = true
    },
    // validate by type and value
    validate (type, value) {
      if (type === 'email') {
        this.newUser.email.valid = this.isEmail(value)
      }
    },
    // check for valid email adress
    isEmail (value) {
      return emailRegExp.test(value)
    },
    // handers for img upload
    onFileChanged (event) {
      console.log('file is changed')
      this.selectedFile = event.target.files[0]
      this.newUser.photo = this.selectedFile.name
    }
  },
  watch: {
    // watching nested property
    'newUser.email.value': function (value) {
      this.validate('email', value)
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
