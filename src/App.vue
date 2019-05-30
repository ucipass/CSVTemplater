<template>
  <div id="app" class="disp-flex-vertical m-0">
    <ModalLogin v-on:loginEvent="login" ref="refModalLogin"/>
    <NavBarMain class="flex-fix"
      v-on:checkedtmpEvent2="checkedtmpEvent"
      v-if="true">
    </NavBarMain>
    <Generator class=flex-auto v-on:loginEvent="login" csvon="csvon" ref="refGenerator"/>
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
  data: function(){ 
    return {
      seen: true,
      csvon: true
    }
  },
  methods: {
    login: function(value){
      console.log("Main", value)

    },
    checkedtmpEvent: function(value){
      console.log("checkedtmpEvent", value)
      this.csvon = value

    },
    begin: function(){
      this.seen = false

    }
  },
  mounted: function(){
    document.title = "CSVTemplater"
    // console.log("APP STARTED!" )
    //     this.$nextTick(() => {
    //   this.$refs.refModalLogin.show()
    //   console.log(this.$refs.refModalLogin.username)
    // })
    
  }
}
</script>

<style>
html,
body {
  height: 100%;
  width: 100%;
  margin: 0
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100%;
  width: 100%;
}

.disp-flex-vertical {
  display: flex;
  flex-flow: column;
  height: 100%;
}
.disp-flex-horizontal {
  display: flex;
  flex-flow: row;
  height: 100%;
}

.flex-fix {
  /* border: 1px dotted grey; */
  flex-grow: 0;
  flex-shrink: 0;
  flex-basis: auto;
}

.flex-auto {
  /* border: 1px dotted grey; */
  flex-grow: 1;
  flex-shrink: 1;
  flex-basis: auto;
}

</style>
