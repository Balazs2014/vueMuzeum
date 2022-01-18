<template>
  <div id="app">
    <h1>:)</h1>
    <table>
      <thead>
        <tr>
          <th>Id</th>
          <th>Person</th>
          <th>Height</th>
          <th>Price</th>
          <th>Operations</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="statue in statues" v-bind:key="statue.id">
          <td>{{ statue.id }}</td>
          <td>{{ statue.person }}</td>
          <td>{{ statue.height }}</td>
          <td>{{ statue.price }}</td>
          <td>
            <button @click="updateStatue(statue.id)">Módosítás</button>
            <button @click="deleteStatue(statue.id)">Törlés</button>
          </td>
        </tr>
        <tr>
          <td> <input type="hidden" v-model="statue.id"> </td>
          <td> <input type="text" v-model="statue.person"> </td>
          <td> <input type="number" v-model="statue.height"> </td>
          <td> <input type="number" v-model="statue.price"> </td>
          <td>
            <button v-if="uj" @click="insertStatue">Létrehozás</button>
            <button v-if="!uj" @click="saveStatue">Mentés</button>
            <button v-if="!uj" @click="cancelUpdate">Mégse</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data() {
    return {
      uj: true,
      mentes: false,
      statue: {
        id: null,
        person: '',
        height: null,
        price: null
      },
      statues: []
    }
  },
  methods: {
    async loadStatues() {
      let Response = await fetch('http://127.0.0.1:8000/api/statues')
      let data = await Response.json()
      this.statues = data
    },
    async insertStatue() {
      this.mentes = 'disabled'
      await fetch('http://127.0.0.1:8000/api/statues', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        },
        body: JSON.stringify(this.statue)
      })
      await this.loadStatues()
      this.mentes = false
      this.resetForm()
    },
    async deleteStatue(id) {
      await fetch(`http://127.0.0.1:8000/api/statues/${id}`, {
        method: 'DELETE'
      })
      await this.loadStatues()
    },
    async updateStatue(id) {
      let Response = await fetch(`http://127.0.0.1:8000/api/statues/${id}`)
      let data = await Response.json()
      this.statue = {...data}
      this.uj = false
    },
    async saveStatue() {
      this.mentes = 'disabled'
      await fetch(`http://127.0.0.1:8000/api/statues/${this.statue.id}`, {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json',
          'Accept': 'application/json'
        },
        body: JSON.stringify(this.statue)
      })
      await this.loadStatues()
      this.mentes = false
      this.resetForm()
    },
    cancelUpdate() {
      this.resetForm()
    },
    resetForm() {
      this.statue = {
        id: null,
        person: '',
        height: null,
        price: null
      }
      this.uj = true
    }
  },
  mounted() {
    this.loadStatues()
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>