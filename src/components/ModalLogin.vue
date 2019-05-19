<template>
  <div>
    <b-modal :id="id" class="text-center" :title="title">
        <b-container>
          <b-row class="my-1">
            <b-col sm="3">
              <label>Username</label>
            </b-col>
            <b-col sm="9">
              <b-form-input id='username' type="text" v-model='username'></b-form-input>
            </b-col>
          </b-row>
          <b-row class="my-1">
            <b-col sm="3">
              <label>Password</label>
            </b-col>
            <b-col sm="9">
              <b-form-input id='password' type="password" v-model="password"></b-form-input>
            </b-col>
          </b-row>                
        </b-container>
        <div slot="modal-footer" class="w-100">
          <p class="float-left"></p>
          <b-button variant="primary" size="sm" class="float-right" @click="login">Login</b-button>
        </div>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: 'ModalLogin',
  props: {
    title: {
      default: "Login",
      type: String
    },
    id: {
      default: "ModalLoginId",
      type: String
    }
  },
  data: function(){
    return{
      username: 'myuser',
      password: 'mypass'
    }
  },
  methods:{
    show: function(){
      this.$bvModal.show(this.id)
    },
    hide: function(){
      this.$bvModal.hide(this.id)
    },
    login: function(){
      this.$emit('loginEvent', {username: this.username, password: this.password} )
    }
  },
  mounted: function () {
    // this.$nextTick(() => {
    //   this.$bvModal.show('modal-1')
    // })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
