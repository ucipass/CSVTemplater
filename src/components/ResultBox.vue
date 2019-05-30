<template>
  <b-form-textarea
        :value="message"
        placeholder="Results of the template and CSV rows combined"
        ></b-form-textarea> 
</template>

<script>
export default {
  name: 'ResultBox',
  props: {
    message: {
      default: "No result...",
      type: String
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
