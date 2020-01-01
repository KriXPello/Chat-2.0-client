<template>
  <div id="home">

    <div id="navbar">
      <p>Вы:</p>
      <button @click="loginDialog = true">{{ name }}</button>
      <img
        v-if="! pullout"
        src="../assets/menu.png"
        alt="Меню"
        @click="pullout = true"
      >
    </div>
    
    <div id="home-body">
      <MessagesBlock
        :messages="messages"
        :send="send"
        :refer="toRefer"
        :name="name"
        @unrefer="unrefer"
      />
      <UsersBlock :users="users" :send="send" @refer="refer" class="ub-1" />
    </div>

    <div id="pullout-overlay" v-if="pullout">
      <div class="po-1" @click="pullout = false"></div>
      <div class="po-2">
        <UsersBlock :users="users" :send="send" @refer="refer" />
      </div>
    </div>

    <LoginOverlay
      v-if="loginDialog"
      :send="send"
      @close="loginDialog = false"
    />

    <div id="loading-overlay" v-if="!connected">
      <div>
        <h2>Подключение...</h2>
        <div class="loader"></div>  
      </div>
    </div>

  </div>
</template>

<script>
import MessagesBlock from '../components/MessagesBlock';
import UsersBlock from '../components/UsersBlock';
import LoginOverlay from '../components/LoginOverlay';

const address = 'ws://localhost:2264';

export default {
  name: 'Home',
  components: {
    MessagesBlock,
    UsersBlock,
    LoginOverlay,
  },
  data: () => {
    return {
      socket: null,
      messages: [],
      users: [],
      connected: false,
      name: '',
      pullout: false,
      loginDialog: true,
      deleteDialog: false,
      toRefer: '',
    }
  },
  methods: {
    connect() {
      try {
        console.log('Подключение...');
        this.connected = false;
        this.pullout = false;
        this.loginDialog = false;
        this.deleteDialog = false;

        const socket = new WebSocket(address);

        socket.onopen = () => {
          this.connected = true;
          this.socket = socket;
          this.loginDialog = true;
        }

        socket.onmessage = message => this.processMessage(message);

        socket.onclose = () => setTimeout(this.connect(), 5000);

        socket.onerror = () => {
          socket.close();
          this.socket.close();
        }
      } catch (err) {}
    },
    processMessage(message) {
      message = JSON.parse(message.data);

      switch (message.event) {
        case 'you': {
          this.name = message.data.name;
          break;
        }
        case 'messages': {
          this.messages = message.data.messages;
          break;
        }
        case 'users': {
          this.users = message.data.users;
          break;
        }
        case 'message': {
          this.messages.push(message.data);
          break;
        }
        case 'loginResult': {
          if (message.data.result == 'done') {
            this.name = message.data.name;
          }
          break;
        }
      }
    },
    send(data) {
      if (data && this.connected) {
        try {
          this.socket.send(JSON.stringify(data));
        } catch (err) {}
      }
    },
    refer(name) { 
      this.toRefer = name;
      this.pullout = false;
    },
    unrefer() { this.toRefer = '' }
  },
  mounted() {
    this.connect();
  }
}
</script>

<style>
#home {
  width: 100%;
  height: 100%;
}
#navbar {
  width: 100%;
  height: 50px;
  border-bottom: solid rgb(216, 216, 216) 0.5px;
  display: flex;
  flex-direction: row;
  align-items: center;
  box-sizing: border-box;
  padding: 8px;
  animation: appear 2s 1 linear;
}
#navbar p { margin-right: 4px; }
#navbar button {
  max-width: calc(100% - 180px);
  height: 30px;
  min-width: 60px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
#navbar img {
  margin-left: auto;
  width: 36px;
  height: 36px;
  padding: 4px;
  border-radius: 50%;
  display: none;
}
#navbar img:hover {
  background-color: rgb(205, 205, 205);
}
#navbar img:active {
  background-color: rgb(170, 170, 170);
  padding: 2px;
}
#home-body {
  width: 100%;
  height: calc(100% - 50px);
  display: flex;
  box-sizing: border-box;
  flex-direction: row;
  align-items: center;
  padding-left: 5%;
  padding-right: 5%;
}
#home-body > * {
  height: 90%;
  box-sizing: border-box;
  border-radius: 4px;
  box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
  border: solid rgb(216, 216, 216) 0.5px;
  animation: appear 1s 1 linear;
}
#messages-block {
  width: calc(100% - 200px);
  display: flex;
  flex-direction: column;
  padding: 6px;
}
#users-block {
  width: 200px;
  margin-left: 4px;
}
#pullout-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(128, 128, 128, 0.5);
  display: none;
  overflow: hidden;
}
#pullout-overlay .po-1 {
  width: calc(100vw - 201px);
  height: 100vh;
}
#pullout-overlay .po-2 {
  position: relative;
  width: 200px;
  height: 100vh;
  box-sizing: border-box;
  background-color: #fff;
  border-left: solid rgb(216, 216, 216) 1px;
  animation: slide-left 200ms 1 ease-out;
}
#loading-overlay {
  position: absolute;
  top: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(128, 128, 128, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}
#loading-overlay div {
  width: 290px;
  height: 200px;
  border-radius: 5%;
  background-color: #fff;
  display: flex;
  flex-direction: column;
  align-items: center;
}
#loading-overlay div div {
  width: 48px;
  height: 48px;
  margin-top: 20px;
  border: solid #000 5px;
  border-radius: 100%;
  border-left-color: rgb(40, 40, 40);
  border-bottom-color: rgb(80, 80, 80);
  border-right-color: transparent;
  animation: rotate 1s infinite linear;
}
input {
  box-sizing: border-box;
  padding: 4px;
  margin-right: 4px;
  border: solid gray 1px;
  outline: none;
  border-radius: 12px;
}
input:focus {
  border: solid skyblue 0.5px;
}
.overlay {
  position: absolute;
  top: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(128, 128, 128, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

@media (max-width: 640px) {
  #home-body {
    padding: 0;
  }
  #home-body > * {
    height: 100%;
    border-radius: 0;
    box-shadow: none;
    border: none;
  }
  #navbar img {
    display: block;
  }
  #messages-block {
    width: 100%;
    padding: 0;
    border-radius: 0;
  }
  #pullout-overlay {
    display: flex;
    flex-direction: row;
  }
  #users-block {
    margin-left: 0;
  }
  .ub-1 {
    display: none;
  }
}

@keyframes appear {
  0% { opacity: 0 }
  100% { opacity: 1 }
}

@keyframes slide-left {
  0% { right: -200px }
  100% { right: 0 }
}

@keyframes rotate {
  0% { transform: rotate(0deg) }
  100% { transform: rotate(360deg) }
}
</style>