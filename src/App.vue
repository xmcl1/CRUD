<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: "App"
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
}
html,
body {
  height: 100%;
  width: 100%;
}
#app,
.home {
  height: 100%;
}
input[type="button"],
button {
  min-width: 60px;
  height: 30px;
  background: #f1eded;
  box-shadow: 2px 2px 5px grey;
  border-radius: 4px;
  outline: none;
  border: none;
}
input[type="button"]:hover {
  background: burlywood;
}
input[disabled="disabled"]:hover {
  background: #f1eded;
}
button:hover {
  background: burlywood;
}
button[disabled="disabled"]:hover {
  background: #f1eded;
}
input[type="button"]:active {
  background: rgb(93, 93, 160);
  color: white;
}
button:active {
  background: rgb(93, 93, 160);
  color: white;
}
input {
  height: 25px;
  border-radius: 4px;
  outline: none;
}
</style>
