<template>
  <section>
    <h1>Home</h1>
    <!-- <Upload /> -->
  </section>
</template>

<script>
// import { mapState } from 'vuex'
// import Upload from '@/components/Upload.vue'
export default {
  // components: { Upload },
  head() {
    return {
      title: 'Page Title',
      meta: [
        // hid is used as unique identifier. Do not use `vmid` for it as it will not work
        {
          hid: 'description',
          name: 'description',
          content: 'My custom description'
        }
      ]
    }
  }
  // computed: {
  //   ...mapState(['userProfile'])
  // }
}
</script>

<style lang="scss" scoped></style>
