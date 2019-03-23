<template>
    <section class="container">
      <div class="login" @click="doLogin">
        [login: {{user_name}}]
      </div>
      <h1>{{title}}</h1>
      <p class="message">{{message}}</p>
      <div v-if="logined">
        <table>
          <tr>
            <th>Message</th>
            <td><input v-model="msg" size="50"></td>
            <td><button @click="add">投稿</button></td>
          </tr>
        </table>
        <hr>
        <ul v-for="(data, key) in json_data">
          <li>
            <div class="list1">{{data.msg}}</div>
            <div class="list2">{{data.user}} ({{data.posted}})</div>
          </li>
        </ul>
      </div>
    </section>
</template>

<script>
  import firebase from 'firebase';
  const config = {
    apiKey: process.env.FIREBASE_API_KEY,
    authDomain: process.env.FIREBASE_AUTH_DOMAIN,
    databaseURL: process.env.FIREBASE_DATABASE_URL,
    projectId: process.env.FIREBASE_PROJECT_ID,
    storageBucket: process.env.FIREBASE_STORAGE_BUCKET,
    messagingSenderId: process.env.FIREBASE_MESSAGING_SENDER_ID
  };
  firebase.initializeApp(config);

  export default {
    data: function () {
      return {
        title: 'Board',
        message: 'ミニ伝言板。最新の投稿を表示します。',
        user_name: '',
        logined: true,
        msg: '',
        page: 0,
        num_per_page: 10,
        json_data: {},
      };
    },
    methods: {
      login: function () {
        const provider = new firebase.auth.GoogleAuthProvider();
        const self = this;
        firebase.auth().signInWithPopup(provider)
          .then(function (result) {
            console.log(result.user);
            self.user = result.user;
            self.user_name = result.user.displayName;
            self.message = 'ログインしました。';
          }).then(() => {
            this.displayList(self);
        });
      },
      displayList: function(self) {
        const db = firebase.database();
        const ref = db.ref('board');
        ref.orderByKey()
          .limitToLast(this.num_per_page)
          .on('value', function (snapshot) {
            if (firebase.auth().currentUser != null) {
              let arr = [];
              const data = snapshot.val();
              for(const item in data) {
                arr.unshift(data[item]);
              }
              console.log(arr);
              self.json_data = arr;
            } else {
              self.json_data = {};
            }
          });
      },
      logout: function () {
        firebase.auth().signOut();
        this.user_name = '';
        this.json_data = {};
        this.message = 'ログアウトしました';
      },
      doLogin: function() {
        if (firebase.auth().currentUser === null) {
          this.login();
        } else {
          this.logout();
        }
      },
      add: function () {
        if (firebase.auth().currentUser === null) {
          alert('ログインしないと投稿できません');
          return;
        }
        const userName = this.getUserName();
        console.log(userName);
        const currentTime = this.getCurrentTime();
        const id = this.getPostId();
        const data = {
          msg: this.msg,
          user: userName,
          posted: currentTime,
        };
        firebase.database().ref('board/' + id).set(data);
        this.msg = '';
        this.message = '投稿しました';
      },
      getUserName: function() {
        const user = firebase.auth().currentUser;
        return user.displayName;
      },
      getCurrentTime: function () {
        const d = new Date();
        return d.getFullYear() + '-' + (d.getMonth() + 1) + '-' + d.getDate() + ' ' + d.getHours() + ':'
          + d.getMinutes() + ':' + d.getSeconds();
      },
      getPostId: function () {
        const d = new Date();
        return d.getTime();
      }
    },
    created: function () {
      if (firebase.auth().currentUser === null) {
        this.login();
      }
      console.log(firebase.auth().currentUser);
    }
  }
</script>

<style>
.login {
  font-weight: bold;
  font-size: 12pt;
  cursor: pointer;
}
.list1 {
  text-align: left;
  font-size: 16pt;
}
.list2 {
  text-align: right;
  font-size: 10pt;
}
.container {
  padding: 5px 10px;
}
h1 {
  font-size: 60pt;
  color: #345980;
}
p {
  padding-top: 5px;
  margin: 10px 0;
  font-size: 20pt;
}
.message {
  font-size: 16pt;
}
div {
  font-size: 14pt;
}
ul {
  margin: 0 10px;
  background-color: aliceblue;
}
li {
  padding: 10px;
  font-size: 14pt;
}
tr th {
  width: 120px;
  background-color: darkblue;
  color: white;
  font-size: 16pt;
}
tr td {
  padding: 5px 10px;
  background-color: #eef;
  font-size: 14pt;
}
hr {
  margin: 10px 0;
}
input {
  font-size: 14pt;
}
button {
  font-size: 14pt;
}
</style>
