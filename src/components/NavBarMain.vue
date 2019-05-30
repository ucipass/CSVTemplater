<template>
  <div>
    <b-navbar toggleable="sm" type="dark" variant="dark">
      <b-navbar-brand v-if='false' href="#">Home</b-navbar-brand>

      <b-navbar-toggle target="nav-collapse" ></b-navbar-toggle>

      <b-collapse size='lg' id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-dropdown left variant="dark" text='Menu'>
            <b-dropdown-item @click="loadExample1Event">Load Example1</b-dropdown-item>
          </b-dropdown>
          <b-form-checkbox v-if="true"
            v-model="checkedcsv"
            @change="checkedcsvEvent"
            name="check-button"
            switch class='text-light m-2'>
            CSV
          </b-form-checkbox>
          <b-form-checkbox v-if="true"
            v-model="checkedtmp" 
            @change="checkedtmpEvent"
            name="check-button"
            switch class='text-light m-2'>
            Template
          </b-form-checkbox>
          <b-form-checkbox v-if="true"
            v-model="checkedresults"
            @change="checkedresultsEvent"
            name="check-button"
            switch class='text-light m-2'>
            Results
          </b-form-checkbox>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
  </div>
</template>

<script>
export default {
  name: 'NavBarMain',
  props: {
    title: {
      default: "Login",
      type: String
    },
    id: {
      default: "NavBarMain",
      type: String
    }
  },
  data: function(){
    return{
      checkedcsv: true,
      checkedtmp: true,
      checkedresults: true
    }
  },
  methods:{
    testfn: function(){
      console.log("TEST")
    },
    checkedtmpEvent: function(){
      this.$root.$emit('checkedtmpEvent', this.checkedtmp )
    },
    checkedcsvEvent: function(){
      this.$root.$emit('checkedcsvEvent', this.checkedcsv )
    },
    checkedresultsEvent: function(){
      this.$root.$emit('checkedresultsEvent', this.checkedresults )
    },
    loadExample1Event: function(){
      this.$root.$emit('loadExample1Event', this.checkedtmp )
      console.log("Example")
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
