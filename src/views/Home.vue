<template>
  <div>
    <div>
      <div>
        <h1>Rejestarcja</h1>
        <span type="error" v-if="registerErrorMessage!=null">{{registerErrorMessage}}</span>
        <span type="error" v-for="error in registerErrors" :key="error">{{error}}</span>
        <span type="success" v-if="registerStatus">Konto utworzone. Możesz się zalogować.</span>
        <input type="Text" v-model="register.email" placeholder="Adres email"></input>
        <input type="Text" v-model="register.firstName" placeholder="Imie"></v-text-field>
        <input type="Text" v-model="register.indexNumber"  placeholder="Numer indeksu"></input>
        <input type="Text" v-model="register.password"  placeholder="Hasło"></input>
        <input type="Text" v-model="register.confirmPassword"  placeholder="Powtórzone hasło"></input>
        <button @click="Register">Zarejestruj</button>
      </div>
      <div>
        <h1>Logowanie</h1>
        <span type="error" v-if="loginErrorMessage!=null">{{loginErrorMessage}}</span>
        <span type="error" v-for="error in loginErrors" :key="error">{{error}}</span>
        <input type="Text" v-model="login.email"  placeholder="Adres email"></input>
        <input type="Text" v-model="login.password"  placeholder="Hasło"></input>
        <button @click="Login">Zaloguj</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Home',
  data: () => {
    return {
      register: {
            email: null,
            firstName: null,
            indexNumber: null,
            password: null,
            confirmPassword: null
      },
      login: {
        email: null,
        password: null
      },
      registerErrors: [],
      registerErrorMessage: null,
      registerStatus: false,
      loginErrors: [],
      loginErrorMessage: null
    }
  },
  methods: {
    Register(){
      axios.post('https://zaliczenie.btry.eu/api/Auth/Register', this.register)
      .then(response=>{
        if(response.data.isSuccess) this.registerStatus=true
      })
      .catch(reason => {
        this.registerErrorMessage=reason.response.data.message
        if(reason.response.data.hasOwnProperty('errors')){
          this.registerErrors=reason.response.data.errors
        }
      })
    },
    Login(){
      axios.post('https://zaliczenie.btry.eu/api/Auth/Login', this.login)
          .then(response=>{
            this.$store.commit('setToken', response.data.message)
            this.$store.commit('setAuth', true)
            this.$store.commit('setEmail', this.login.email)
            this.$router.push({name: 'Courses'})
          })
          .catch(reason => {
            this.loginErrorMessage=reason.response.data.message
            if(reason.response.data.hasOwnProperty('errors')){
              this.loginErrors=reason.response.data.errors
            }
          })
    }
  },
  mounted(){
    if(this.$store.state.isAuth){
      this.$router.push({name: 'Courses'})
    }
  }
}
</script>

<style scoped >

	input{
		display:block;
	}

  
  button{
	  
	  padding:20px;
    background-color: #a5d5ee;
    color: #14658f;
    border: 1px solid #2f7ea7;
  }

</style>
