<template>
    <div class="container">
        <div class="row">
            <div class="col-xs-12 col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3">
                <h1>Vue Weather Search</h1>
                  <div class='form-group'>
                    <label>Username</label>
                    <input class='form-control' type='text' v-model='user.username'>
                  </div>
                  <div class='form-group'>
                    <label>Mail</label>
                    <input class='form-control' type='text' v-model='user.email'>
                  </div>
                    <button class='btn btn-primary' @click="submit">Save User</button>
                    <button class='btn btn-warning' @click="fetchData">Get User</button>
                  <ul>
                    <li class="list-group" v-for="eachUser in users">{{eachUser.username}} - {{eachUser.email}}
                  </ul><br/>
                  <div class='form-group'>
                    <label>City</label>
                    <input class="form-control" type="text" v-model='city'>
                  </div>
                  <div class='form-group'>
                    <label>State "ex.CA"</label>
                    <input class="form-control" type="text" v-model='state'>
                  </div>
                  <div>
                    <button class='btn btn-success' @click="fetchWeather">Get Weather</button>
                    <button class='btn btn-success' @click="saveWeather">save Weather</button><br/>
                       <span v-if="renderWeather">Weather today in {{weather.display_location.city}} is {{weather.temp_f}} F</span>
                  </div>
                  <div>

                  </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
      data(){
        return {
          user: {
            username: '',
            email: ''
          },
          users: [],
          city: '',
          state: '',
          weather: {},
          renderWeather: false
        }
      },
      methods: {
        submit() {
          this.$http.post('https://my-vue-project-c1bdb.firebaseio.com/data.json', this.user)
          .then((resp) => {
          console.log(resp)
          }, (error) => {
            console.log(error)
          })
        },
        fetchData() {
          this.$http.get('https://my-vue-project-c1bdb.firebaseio.com/data.json')
          .then((resp) => {
            return resp.json()
          })
          .then((data) => {
          console.log(data)
          const resultArray = []
          for(var key in data) {
          resultArray.push(data[key])
          }
          this.users = resultArray
          })
        },
        fetchWeather() {
          this.$http.get(`http://api.wunderground.com/api/b5586bdb0a93ff67/conditions/q/${this.state}/${this.city}.json`)
          .then((resp) => {
            return resp.json()
          })
          .then((data) => {
            const result = data.current_observation
            this.weather = result
            this.renderWeather = true
            console.log(this.weather)
          })
        },
        saveWeather() {
          this.$http.post('https://my-vue-project-c1bdb.firebaseio.com/data.json', {
            location: this.weather.display_location.city,
            weather: this.weather.temp_f,
            createdAt: new Date()
          })
          .then((resp) => {
          console.log(resp)
          }, (error) => {
            console.log(error)
          })
        }
      }
    }
</script>

<style>
</style>
