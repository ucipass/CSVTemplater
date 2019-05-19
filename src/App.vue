<template>
  <div id="app" class="m-0">
    <div>
      <b-jumbotron 
        v-if="seen"
        header="CSV Templater"
        lead="Generate text files by combining rows from a CSV file with a template">
        <b-button 
          variant="primary"
          @click='begin'
        >Begin</b-button>
      </b-jumbotron>
    </div>
    <div v-if="!seen">
      <NavBarMain></NavBarMain>
      <ModalLogin v-on:loginEvent="login" ref="refModalLogin"/>
      <Generator v-on:loginEvent="login" ref="refGenerator"/>
    </div>

  </div>
</template>

<script>
import NavBarMain from './components/NavBarMain.vue'
import ModalLogin from './components/ModalLogin.vue'
import Generator from './components/Generator.vue'

export default {
  name: 'app',
  components: {
    ModalLogin,Generator,NavBarMain
  },
  data: function(){ return {seen: true}},
  methods: {
    login: function(value){
      console.log("Main", value)

    },
    begin: function(){
      this.seen = false

    }
  },
  mounted: function(){
    // console.log("APP STARTED!" )
    //     this.$nextTick(() => {
    //   this.$refs.refModalLogin.show()
    //   console.log(this.$refs.refModalLogin.username)
    // })
    
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
