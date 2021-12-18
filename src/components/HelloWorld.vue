<template>
  <div class="login">
    <div class="login-header">
      <h1>Login</h1>
    </div>
    <div class="login-body">
      <form @submit.prevent="login">
        <div class="form-group">
          <label for="username">Username</label>
          <input
            type="text"
            value="mod@mod.com"
            class="form-control"
            id="username"
            v-model="username"
          />
        </div>
        <div class="form-group">
          <label for="password">Password</label>
          <input
            type="password"
            value="mod"
            class="form-control"
            id="password"
            v-model="password"
          />
        </div>
        <button type="submit" class="btn btn-primary">Login</button>
      </form>
      <button class="btn btn-primary" @click="showprofile">
        Show my profile
      </button>
      <button type="submit" class="btn btn-primary" @click="logout">
        Logout
      </button>

      <div>
        <p>{{ message }}</p>
      </div>
      <div v-if="profile">
        <ul>
          <li>
            your ID : <b>{{ profile.user.id }} </b>
          </li>
          <li>
            your Name : <b>{{ profile.user.first_name }} {{ profile.user.last_name }}</b>
          </li>
          <li>
            your District ID : <b>{{ profile.user.district_id }}</b>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
const axios = require("axios");
// const got = require("./node_modules/got");
// import got from '@/got'

export default {
  name: "HelloWorld",
  data() {
    return {
      username: "",
      password: "",
      message: "you are not logged in",
      token: "",
      ref_token: "",
      profile: false,
    };
  },
  props: {
    msg: String,
  },
  methods: {
    login() {
      const baseUrl = "http://localhost:8000";
      // const baseUrl = "https://ptb1prod.herokuapp.com";

      axios
        .post(`${baseUrl}/api/users/auth/email/`, {
          email: this.username,
          password: this.password,
        })
        .then((res) => {
          if (res.status === 200) {
            this.message = `You are logged in!`;
            this.token = res.data.accessToken;
            // this.token = res.headers["x-token"];
            this.ref_token = res.data.refreshToken;
            console.log(res.data);
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },

    // async login2() {
    //   try {
    //     const response = await got(
    //       `${baseUrl}/api/users/auth/email/`,
    //       { json: true }
    //     );
    //     console.log(response.body.name);
    //   } catch (error) {
    //     console.log(error.response.body);
    //   }
    // },

    showprofile() {
      const baseUrl = "http://localhost:8000";

      axios
        .get(`${baseUrl}/api/users/me/`, {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        })
        .then((res) => {
          this.profile = res.data;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    logout() {
      const baseUrl = "http://127.0.0.1:8000";
      console.log(this.token, "TOKEN");
      axios
        .post(`${baseUrl}/api/auth/revoke/`, {
          token: this.ref_token,
        })
        .then((res) => {
          console.log(res.status);
          this.message = `You are logged out!`;
        })
        .catch((err) => {
          console.log(err.response.data);
        });
    },
  },
};
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