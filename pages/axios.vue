<template>
   <section class="container">
     <h1>{{title}}</h1>
     <p>{{message}}</p>
     <table>
       <tr>
         <th>Email</th>
         <td><input v-model="email"></td>
       </tr>
       <tr>
         <th>Name</th>
         <td><input v-model="username"></td>
       </tr>
       <tr>
         <th>Age</th>
         <td><input type="number" v-model="age"></td>
       </tr>
       <tr>
         <th>Tel</th>
         <td><input v-model="tel"></td>
       </tr>
       <tr><th></th><td>
         <button @click="addData">Click</button>
       </td></tr>
     </table>
     <hr>
     <ul v-for="(data, key) in json_data">
       <li>
         <strong>{{key}}</strong>
         <button @click="deleteData(key)">Delete</button>
         <br>
         {{data}}
       </li>
     </ul>
   </section>
</template>

<script>
  import axios from 'axios';

  const url = "https://vue-sample-baa0f.firebaseio.com/person";

  export default {
    data: function () {
      return {
        title: 'Axios',
        message: 'axios sample.',
        email: '',
        username: '',
        tel: '',
        age: 0,
        json_data: {},
      };
    },
    methods: {
      addData: function() {
        const add_url = url + '/' + this.email + '.json';
        const data = {
          'name': this.username,
          'age': this.age,
          'tel': this.tel
        };
        axios.post(add_url, data).then((res) => {
          this.clearData();
          this.getData();
        }).catch((error) => {
          this.message = 'ERROR!';
          console.log(error.message);
        });
      },
      deleteData: function(email) {
        // TODO URLをaddと共通化する
        const delete_url = url + '/' + email + '.json';
        axios.delete(delete_url).then((res) => {
          this.message = email + 'を削除しました';
          this.getData();
        });
      },
      getData: function() {
        axios.get(url + '.json').then((res) => {
          this.message = 'get all data';
          this.json_data = res.data;
        }).catch((error) => {
          this.message = 'ERROR!';
          console.log(error.message);
          this.json_data = {};
        });
      },
      clearData: function () {
        this.email = '';
        this.username = '';
        this.age = 0;
        this.tel = '';
      }
    },
    created: function() {
      this.getData();
    }
  }
</script>

<style>
ul {
  margin: 0 10px;
  background-color: aliceblue;
}
li {
  padding: 10px;
  font-size: 16pt;
}
input {
  font-size: 14pt;
}
button {
  font-size: 14pt;
}
tr th {
  width: 150px;
  background-color: darkblue;
  color: white;
  font-size: 16pt;
}
tr td {
  padding: 5px 10px;
  background-color: #eef;
  font-size: 14pt;
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
  font-size: 20pt;
}
div {
  font-size: 14pt;
}
pre {
  padding: 10px;
  font-size: 18pt;
  background-color: #efefef;
  white-space: pre-wrap;
}
hr {
  margin: 10px 0;
}
</style>
