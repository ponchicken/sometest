<template>
  <div class="about">
    <div class="header">
      <button class="btn" @click="generateNewAnimal">Generate new animal</button>
      <button class="btn" @click="getAllAnimals">Show all animals</button>
    </div>
    <div class="animal" v-if="!shawAll">
      <img :src="`animals/${animal.img}.png`" alt="">
      {{ animal.who}} {{ animal.how}} {{ animal.what }} {{ animal.where }}
    </div>
    <div class="animals" v-if="shawAll">
      <div class="animal" v-for="(animal, i) in animals" :key="i">
        <img :src="`animals/${animal.img}.png`" alt="">
        {{ animal.who}} {{ animal.how}} {{ animal.what }} {{ animal.where }}
      </div>
    </div>
  </div>
</template>


<script>

export default {
  data: () => ({
    addedUser: '',
    socket: new WebSocket("ws://localhost:8081"),
    animal: {},
    animals: [],
    shawAll: false
  }),
  computed: {
  },
  methods: {
    generateNewAnimal: function () {
      this.shawAll = false
      this.socket.send(JSON.stringify({
        type: 'generateAnimal'
      }))
    },
    getAllAnimals: function () {
      this.shawAll = true
      this.socket.send(JSON.stringify({
        type: 'getAllAnimals'
      }))
    }
  },
  created: function () {
    console.log(this.some)
    this.socket.onopen = () => console.log('WS подключенно')
    this.socket.onclose = (event) =>  console.log('соеденение закрыто причина: ' + event)
    this.socket.onmessage = (event) => {
      let data = JSON.parse(event.data)
      if (data.type === 'single') {
        this.animal = data.data
      }
      else if (data.type === 'all') {
        this.animals = data.data
      }
    }
  }
}
</script>


<style lang="scss">

  .header{
    display: flex;
    padding: 20px;
    background: rgb(107, 88, 114);
    justify-content: center;
    &>*{
      margin: 0 20px;
    }
  }
  .animal{
    font-size: 22px;
    padding: 10px;
    display: flex;
    background: #c85656;
    align-items: center;
    justify-content: center;
     
    img{
      margin-right: 5px;
    }
  }

  .animals{
    display: grid;
    align-items: center;
    grid-gap: 10px;
    padding: 20px;

    .animal{
      background: #c85656;
      justify-content: flex-start;
    }
  }

</style>