<template>
  <div id="app">
<!--    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
-->

    <router-view/> 

  </div>
</template>

<style lang="scss">

h2{
  font-weight: 400;
}



#app {
  font-family: 'Source Sans Pro', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: white;

  height: 100vh;
  padding: 32px;
}

/* #nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
} */

body{
  margin: 0;
  background-color: black;
}
</style>
