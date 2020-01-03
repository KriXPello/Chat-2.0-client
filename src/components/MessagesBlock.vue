<template>
  <div id="messages-block">
    
    <div id="messages-list">
      <transition-group name="list">
        <div
          class="message-container"
          v-for="message in messages"
          :key="message._id"
          :style="message.text.includes('@' + name)
            ? 'background-color: rgb(120, 120, 120, 0.4)'
            : ''
          "
        >
          <b class="message-name">{{ message.name }}</b>
          <p class="message-text"> {{ message.text }}</p>
        </div>  
      </transition-group>
      
    </div>

    <div class="input-block">
      <input
        ref="input"
        type="text"
        v-model="text"
        v-on:keydown.enter="sendMessage()"
      >
      <button @click="sendMessage()">
        <img src="../assets/send.png" alt="ОК">
      </button>
    </div>

  </div>
</template>

<script>
export default {
  name: 'MessagesBlock',
  props: [ 'messages', 'send', 'refer' ],
  data: () => {
    return {
      text: '',
    }
  },
  methods: {
    sendMessage() {
      const obj = {
        event: 'newMessage',
        data: {
          text: this.text
        }
      };
      this.send(obj);

      this.text = '';

      this.$refs.input.focus();
    }
  },
  watch: {
    messages() {
      setTimeout(() => {
        const block = this.$el.querySelector('#messages-list');

        block.scrollTop = block.scrollHeight;
      }, 10);
    },
    refer(name) {
      if (!! name && ! this.text.includes('@' + name)) {
        name = '@' + name + ' ';

        this.text += name;

        this.$refs.input.focus();
      }
      
      this.$emit('unrefer');
    }
  }
}
</script>

<style>
#messages-list {
  width: 100%;
  height: calc(100% - 54px);
  background-color: rgb(241, 241, 241);
  border-radius: 4px;
  box-sizing: border-box;
  padding: 6px;
  overflow-x: hidden;
  overflow-y: auto;
}
.input-block {
  width: 100%;
  height: 50px;
  margin-top: 4px;
  background-color: #fff;
  display: flex;
  flex-direction: row;
  align-items: center;
  padding-top: 0;
  padding-bottom: 0;
}
.input-block input {
  width: 100%;
  height: 41px;
}
.message-container {
  width: auto;
  height: auto;
  border-radius: 4px;
  background-color: rgb(241, 241, 241);
  box-sizing: border-box;
  padding: 6px;
  padding-top: 4px;
  margin-top: 2px;
}
.message-name {
  word-break: break-all;
}
.message-text {
  word-break: break-all;
}
p {
  box-sizing: border-box;
  margin: 4px;
  margin-left: 8px;
}
@media (max-width: 640px) {
  #messages-list {
    height: calc(100% - 50px);
    border-radius: 0;
    padding: 0;
  }
  .input-block {
    box-sizing: border-box;
    padding: 4px;
    margin-top: 0;
  }
  .message-container {
    border-radius: 0;
  }
}

/* Анимации transition Vue */
.list-enter-active, .list-leave-active {
  transition: all 500ms;
}
.list-enter, .list-leave-to  {
  opacity: 0;
  transform: translateX(30px);
}
/*_____________________________________*/
</style>