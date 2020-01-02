<template>
  <div id="users-block">

    <div class="remove-block">
      <button @click="confirmation = !confirmation">Удалить все сообщения</button>
      <transition name="confirmation">
        <div v-if="confirmation" class="remove-confirmation">
          <b>Вы действительно хотите удалить все сообщения у всех пользователей?</b>
          <div>
            <button @click="removeMessages()">Да</button>
            <button @click="confirmation = false">Нет</button>  
          </div>
        </div>  
      </transition>
    </div>

    <div class="users-list">
      <div
        class="user-container"
        v-for="(user, i) in users"
        :key="i"
      >
        <button @click="refer(user.name)">{{ user.name }}</button>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name: 'UsersBlock',
  props: [ 'users', 'send' ],
  data: () => {
    return {
      confirmation: false,
    }
  },
  methods: {
    removeMessages() {
      const obj = {
        event: 'deleteMessages',
        data: {}
      }
      this.send(obj);

      this.confirmation = false;
    },
    refer(name) {
      this.$emit('refer', name);
    }
  }
}
</script>

<style>
.remove-block {
  height: 43px;
  padding: 6px;
  box-sizing: border-box;
  border-bottom: solid rgb(216, 216, 216) 0.5px;
}
.remove-block button {
  width: 100%;
  height: 30px;
  z-index: 3;
}
.users-list {
  height: calc(100% - 43px);
  width: 100%;
  box-sizing: border-box;
  padding: 4px;
  overflow-y: auto;
  overflow-x: hidden;
}
.user-container {
  margin-top: 5px;
  box-sizing: border-box;
  padding: 6px;
  padding-top: 0;
}
.user-container button {
  width: 100%;
  height: auto;
  box-sizing: border-box;
  padding: 5px;
  border-radius: 2px;
  word-break: break-all;
}
.remove-confirmation {
  position: relative;
  width: 100%;
  height: 140px;
  background-color: #fff;
  z-index: 1;
  box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.3);
  box-sizing: border-box;
  padding: 4px;
  border-radius: 6px;
}
.remove-confirmation div {
  height: calc(100% - 71px);
  box-sizing: border-box;
  display: flex;
  flex-direction: row;
}
.remove-confirmation div button {
  width: 60px;
  margin: auto;
  margin-bottom: 4px;
}

/* Анимации transition Vue */
.confirmation-enter-active, .confirmation-leave-active {
  transition: all 1s;
}
.confirmation-enter, .confirmation-leave-to {
  opacity: 0;
  transform: translateY(-100px);
}

@media (max-width: 640px) {
  .confirmation-enter-active, .confirmation-leave-active {
    transition: all 500ms;
  }
  .confirmation-enter, .confirmation-leave-to {
    opacity: 0;
    transform: none;
  }
}
/*________________________________________________________*/
</style>