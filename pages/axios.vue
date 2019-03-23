<template>
   <section class="container">
     <h1>{{title}}</h1>
     <p>{{message}}</p>
     <input v-model="find">
     <button @click="getData">Click</button>
     <hr>
     <ul>
       <li>{{json_data}}</li>
     </ul>
   </section>
</template>

<script>
  import axios from 'axios';

  const listing_url = "https://vue-sample-baa0f.firebaseio.com/person.json/";
  const base_url = "https://vue-sample-baa0f.firebaseio.com/person.json?orderBy=%22$key%22&equalTo=%22";

    export default {
    data: function () {
      return {
        title: 'Axios',
        message: 'axios sample.',
        find: '',
        json_data: {},
      };
    },
    asyncData: async function() {
      const result = await axios.get(listing_url);
      return { json_data: result.data}
    },
    methods: {
      getData: function() {
        const id_url = base_url + this.find + '%22';
        axios.get(id_url).then((res) => {
          this.message = 'get ID=' + this.find;
          this.json_data = res.data;
        }).catch((error) => {
          this.message = 'ERROR!';
          console.log(error.message);
          this.json_data = {};
        });
      }
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
