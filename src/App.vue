<template>
  <div id="app">
    <div class="panel panel-default">
      <div class="panel-heading">
        <h3 class="panel-title">Add New Books</h3>
      </div>
      <div class="panel-body">
         <form id="form" class="form-inline" v-on:submit.prevent="addBook">
          <div class="form-group">
            <label for="bookTitle">Title:</label>
            <input type="text" id="bookTitle" class="form-control" v-model="newBook.title">
          </div>
          <div class="form-group">
            <label for="bookAuthor">Author:</label>
            <input type="text" id="bookAuthor" class="form-control" v-model="newBook.author">
          </div>
          <input type="submit" class="btn btn-primary" value="Add Book">
        </form>
      </div>
    </div>
  <form class="vue-form" @submit.prevent="submit">

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
               :class="{ email , error: !email.valid }"
               v-model="newUser.email.value">
      </div>

      <div>
        <label class="label" for="textarea">Message with Counter</label>
        <textarea class="message" name="textarea" id="textarea" required=""
                  v-model="message.text"
                  :maxlength="message.maxlength"></textarea>
        <span class="counter">{{ message.text.length }} / {{ message.maxlength }}</span>
      </div>
      <div>
        <input type="submit" value="Send Form" v-on:submit.prevent="addUser">
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
let booksRef = db.ref('books')
let usersRef = db.ref('users')
let emailRegExp = /^[a-zA-Z0-9.!#$%&'*+\/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$/;

export default {
  name: 'App',
  firebase: {
    books: booksRef,
    users: usersRef
  },
  data: function() {
    return {
      newBook: {
        title: '',
        author: ''
      },
      newUser: {
        name: "i love the smell of tea in the morning",
        email: {
          value: "tearocks.oe",
          valid: true
        }
      },
      name: "John Doe",
      email: {
        value: "jo@hnd.oe",
        valid: true
      },
      message: {
        text: `Dear Mr. President,\n...`,
        maxlength: 255
      },
      submitted: false
    };
  },
  methods: {
    // send data to firebase
    addBook: function () {
      booksRef.push(this.newBook);
      this.newBook.title = '';
      this.newBook.author = '';
    },
    addUser: function () {
      usersRef.push(this.newUser);
      this.newUser.name = '';
      this.newUser.email = '';
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
