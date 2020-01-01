<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<style>
#app {
  height: 100%;
  width: 100%;
}
button {
  box-sizing: border-box;
  border: none;
  outline: none;
  border-radius: 2px;
  background-color: rgb(241, 241, 241);
  border-radius: 12px;
}
button:hover {
  background-color: rgb(199, 199, 199);
  transition: 200ms;
}
button:active {
  background-color: rgb(143, 143, 143)
}
</style>
