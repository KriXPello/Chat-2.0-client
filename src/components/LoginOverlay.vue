<template>
  <div class="overlay">

    <div class="login-dialog">
      <div class="close-button-row">
        <img src="../assets/close.png" alt="Закрыть" @click="close()">
      </div>
      <h3>Введите имя</h3>
      <input
        type="text"
        class="login-input"
        v-model="newName"
        @keydown.enter="save()"
        autofocus
      >
      <button class="login-button" @click="save()">Сохранить</button>
    </div>

  </div>
</template>

<script>
export default {
  name: 'LoginDialog',
  props: [ 'send' ],
  data: () => {
    return {
      newName: '',
    }
  },
  methods: {
    save() {
      if (!! this.newName.trim()) {
        const obj = {
          event: 'login',
          data: {
            name: this.newName,
          }
        };
        this.send(obj);

        localStorage.name = this.newName;

        this.newName = '';

        this.close();
      }
    },
    close() { this.$emit('close') },
  },
  mounted() {
    if (localStorage.name) {
      this.newName = localStorage.name;
    }
  }
}
</script>

<style>
.login-dialog {
  width: 40%;
  min-width: 300px;
  max-width: 400px;
  height: 250px;
  background-color: #fff;
  box-sizing: border-box;
  padding: 6px;
  border-radius: 12px;
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3);
  display: flex;
  flex-direction: column;
  align-items: center;
  animation: appear 500ms 1 ease-out;
}
.close-button-row {
  height: 0px;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: flex-end;
}
.close-button-row img {
  height: 28px;
  min-width: 28px;
  position: sticky;
  right: 0;
  top: 0;
  box-sizing: border-box;
  padding: 2px;
  border-radius: 50%;
}
.close-button-row img:hover {
  background-color: rgb(205, 205, 205);
}
.close-button-row img:active {
  background-color: rgb(170, 170, 170);
}
.login-input {
  margin-top: 20px;
  height: 40px;
  width: 80%;
  font-size: 16px;
}
.login-button {
  margin-top: auto;
  height: 30px;
  width: 50%;
  font-size: 18px;
}
</style>