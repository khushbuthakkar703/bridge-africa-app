<template>
  <div id="app" class="" ref="formContainer">
    <router-view />
  </div>
</template>
<script>
/* eslint-disable */

export default {
  data() {
    return {
      messages: [],
      users: [],
      userName: '',
      isLogged: false
    }
  },
  methods: {
      sendMessage: function(message) {
        console.log('message', message)
        if (message) {
          socket.emit('send-msg', { message: message, user: this.userName });
        }
      },
      setName: function(userName) {
        this.userName = userName;
        this.isLogged = true;
        socket.emit('add-user', this.userName);
      },
      scrollToEnd: function() {
        var container = this.$el.querySelector('.messages');
        console.log(container);
        console.log(this.$el);
        container.scrollTop = container.scrollHeight;
      }
  },
  updated() {
    this.scrollToEnd();
  }
};
</script>


<style lang="less">


@import "./assets/css/main.css";
@import "./assets/css/style.css";
@import "./assets/css/bootstrap.css";

@import "./assets/icon/iconfont.css";



  @import "https://pro.fontawesome.com/releases/v5.10.0/css/all.css";


#app {
  font-family: Open Sans, Helvetica Neue, Helvetica, Arial, sans-serif;    
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-left: 5px;
}
.primary {
  color: #e75c18;
}
.primary-bg {
  background-color: #e75c18;
}
.green {
  color: #32a400;
}
.green-bg {
  background-color: #32a400;
}
.yellow {
  color: #ffd200;
}
.yellow-bg {
  background-color: #ffd200;
}
.nav-link {
  color: #000;
}
li .nav-link:hover{
  background-color: #e75c18;
  color: #fff;
}
.nav-pills .nav-link.active,
.nav-pills .show > .nav-link {
  background-color: #e75c18;
}

.alert-primary {
  background-color: #e9a07c;
}
.red {
  color: #ff0000;
}
.red-bg {
  background-color: #ff0000;
}

.purple {
  color: #6263;
}
.purple-bg {
  background-color: #6263;
}
.card-header{
  border: none;
  background-color: red;
}
#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
.toggle {
  color: #fff;
  background-color: #000;
  border-color: #005cbf;
}

.violet {
  color: #630460;
}

@media only screen and (max-width: 768px) {
  .nav-link {
    font-size: 9px;
  }
}
</style>
