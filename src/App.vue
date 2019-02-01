<template>
  <div id="app">
    <img src="./assets/logo.png">
    <HelloWorld/>
  </div>
</template>

<script>
import Firebase from 'firebase'
let config = {
    apiKey: "AIzaSyCMDAI881ToJhye3bPCJfjkgw9sc9AU4Ho",
    authDomain: "contactformvue.firebaseapp.com",
    databaseURL: "https://contactformvue.firebaseio.com",
    projectId: "contactformvue",
    storageBucket: "contactformvue.appspot.com",
    messagingSenderId: "86941929496"
  };

let app = Firebase.initializeApp(config)
let db = app.database()
let booksRef = db.ref('books')

import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',
  components: {
    HelloWorld
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
</style>
