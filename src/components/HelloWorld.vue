<template>
  <div class="login">
    <div class="login-header">
      <h1>Login</h1>
    </div>
    <div class="login-body">
      <form @submit.prevent="login">
        <div class="form-group">
          <label for="username">Username</label>
          <input type="text" class="form-control" id="username" v-model="username">
        </div>
        <div class="form-group">
          <label for="password">Password</label>
          <input type="password" class="form-control" id="password" v-model="password">
        </div>
        <button type="submit" class="btn btn-primary">Login</button>
      </form>
      <button class="btn btn-primary" @click="showprofile">Show my profile</button>
        <button type="submit" class="btn btn-primary" @click="logout" >Logout</button>

      <div>
        <p>{{ message }}</p>
      </div>
      <div v-if="profile">
        <ul>
          <li>your id : {{profile.id}}</li>
          <li>your asset : {{profile.current_assets}}</li>
          <li>your balance : {{profile.balance}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
const axios = require('axios');


export default {
  name: 'HelloWorld',
  data () {
    return {
      username: '',
      password: '',
      message:'you are not logged in',
      token:'',
      ref_token:'',
      profile:false
    }
  },
  props: {
    msg: String
  },
  methods: {
    login() {
      const baseUrl='http://127.0.0.1:8000'
      axios.post(`${baseUrl}/api/auth/`, {
        email: this.username,
        password: this.password
      }).then(res => {
        if (res.status === 200) {
          this.message = `You are logged in!`
          this.token = res.data.access
          this.ref_token = res.data.refresh
        }
      }).catch(err => {
        console.log(err);
      })
    },

    showprofile() {
        const baseUrl='http://127.0.0.1:8000'
     
        axios.get(`${baseUrl}/api/user/me/`,{
        headers:
      {
        Authorization: `Bearer ${this.token}`
        }
      
      }).then(res =>{
        this.profile = res.data
   
      }).catch(err =>{
        console.log(err);
      })

      },
      logout(){
      const baseUrl='http://127.0.0.1:8000'
      console.log(this.token,"TOKEN")
      axios.post(`${baseUrl}/api/auth/revoke/`, {
        token: this.ref_token
      }).then(res => {
        console.log(res.status)
          this.message = `You are logged out!`
        
      }).catch(err => {
        console.log(err.response.data);
      })
      }
    }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>