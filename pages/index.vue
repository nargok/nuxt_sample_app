<template>
  <section class="container">
    <h1>{{title}}</h1>
    <p>{{$store.state.message}}</p>
    <hr>
    <pre>{{now}}</pre>
    <hr>
    <div class="link"
      @click.exact="$store.commit('count', 1)"
      @click.shift="$store.commit('count', 2)">
      <a @click.stop="$store.commit('reset')">
        clicked: {{ $store.state.counter }}
      </a>
    </div>
    <hr>
    <router-link to="/other">Go to Other</router-link>
  </section>
</template>

<script>
export default {
  data: function () {
    return {
      title: 'Hello',
      message: 'this is message',
      now: 'wait...'
    };
  },
  created: function () {
    setInterval(() => {
      var d = new Date();
      this.now = d.getHours()
        + ':' + d.getMinutes()
        + ':' + d.getSeconds();
    }, 1000);
  },
  methods: {
    doAction: function () {
      this.$store.state.counter++;
    }
  },
};
</script>

<style>
a {
  font-size: 16pt;
}

.link {
  background-color: #def;
  padding: 10px;
}

.container {
  padding: 10px;
}

h1 {
  font-size: 60pt;
  color: #345980;
}

p {
  padding-top: 5px;
  font-size: 20pt;
}

pre {
  padding: 10px;
  font-size: 18pt;
  background-color: #efefef;
}

hr {
  margin: 10px 0;
}

</style>
