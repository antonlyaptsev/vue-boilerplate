<template>
  <div id="app">
    <v-app v-if="isLoggedIn" id="admin-panel" top-toolbar footer>
      <v-toolbar>
        <v-toolbar-side-icon />
        <v-toolbar-title>Toolbar</v-toolbar-title>
        <v-toolbar-items @click="logout">
          <v-toolbar-item ripple>LogOut</v-toolbar-item>
        </v-toolbar-items>
      </v-toolbar>
      <main>
        <v-content>
          <v-container fluid>
            <router-view></router-view>
          </v-container>
        </v-content>
      </main>
      <v-footer>Footer</v-footer>
    </v-app>
    <v-app v-else class="wrapper">
      <v-container class="login" fluid>
        <v-text-field
          v-model="username"
          label="Username"
          name="username"
          ref="username"
          :rules="[invalidUsername]">
        </v-text-field>
        <v-text-field
          v-model="password"
          label="Password"
          name="password"
          type="password"
          ref="password"
          :rules="[invalidPassword]">
        </v-text-field>
        <v-btn block secondary dark @click.native="login">LOGIN</v-btn>
      </v-container>
    </v-app>
  </div>
</template>

<script>
  import 'assets/material_icons.css'
  import 'vuetify/dist/vuetify.min.css'

  export default {
    name: 'app',
    data () {
      return {
        username: '',
        password: '',
        loginError: false
      }
    },
    computed: {
      isLoggedIn () {
        return !!this.$root.state.userId
      }
    },
    methods: {
      invalidUsername () {
        return !this.loginError || ''
      },
      invalidPassword () {
        return !this.loginError || 'The username and password you entered don\'t match'
      },
      login () {
        this.axios.post('/api/admin/login', {
          username: this.username,
          password: this.password
        })
        .then(({ data }) => {
          this.username = ''
          this.password = ''
          this.$root.setUserId(data.user.id)
        })
        .catch(() => {
          this.loginError = true
          this.$refs.username.validate()
          this.$refs.password.validate()
        })
      },
      logout () {
        this.axios.get('/api/admin/logout')
        .then(() => {
          this.$root.clearUserId()
        })
        .catch(console.error)
      }
    }
  }
</script>

<style scoped>
 .wrapper {
   display: flex;
   flex-direction: column;
   justify-content: center;
 }

 .login {
   width: 286px;
 }

 .login .input-group {
   margin: 0;
 }
</style>
