<template>
  <section>
    <!-- <template v-if="photo"> -->
    <img
      :id="photo.filename"
      :src="photo.url"
      class="img-fluid thumbnail rounded"
      :alt="photo.title"
    />
    <h1 class="h3">{{ photo.title }}</h1>
    <!-- <Tags :photo="photo" />
      <p>Share edit close delete flag</p>
      <p>Viewed 23 times</p>
      <button @click="hideAnnos">hide annotations</button>
      <button @click="showAnnos">show annotations</button>
      <button @click="highlightAnno">highlight anno</button>
      <b-button variant="link" class="text-success mb-2" v-if="photo.status == 'Solved'"> <b-icon icon="check-circle" aria-hidden="true"></b-icon> Solved! </b-button>
      <b-button
        variant="link"
        v-b-popover.hover.top="'Accept this answer if your question has been solved or you are satisfied with the responses'"
        class="text-secondary mb-2"
        v-if="photo.status == 'Unsolved'"
      >
        <b-icon icon="check-circle" aria-hidden="true"></b-icon> Mark as Solved
      </b-button>

      <section id="description" class="pb-3">
        <SpinButton @spinButton="spinButton" :vertical="vertical" class="d-sm-none w-50" />
        <div class="row">
          <div class="col-sm-1">
            <SpinButton @spinButton="spinButton" class="d-none d-sm-block" />
          </div>
          <p class="col-sm-11">{{ photo.description }}</p>
        </div>
      </section>
      <AnnotationsCard :annotations="annotations" class="pb-3" />

      <p>Asked by {{ photo.username }} on {{ photo.createdAt ? photo.createdAt.toDate().toDateString() : '' }}</p>
      <CommentsCard class="pb-3" />
      <Contributors />

      <p>Know someone who can answer? Share a link</p>
    </template> -->
    <!-- User that submitted this profile:
    <nuxt-link to="/users/1">
      Historyman
    </nuxt-link> -->
  </section>
</template>

<script>
import { Annotorious } from '@recogito/annotorious'
export default {
  // async asyncData({ $axios, error, params }) {
  //   try {
  //     const response = await $axios.get(
  //       'https://jsonplaceholder.typicode.com/posts/' + params.id
  //     )
  //     return { post: response.data }
  //   } catch (e) {
  //     error({
  //       statusCode: 503,
  //       message: 'Unable to fetch post #' + params.id
  //     })
  //   }
  // },
  data() {
    return {
      photo: {},
      anno: {},
      title: this.$route.params.title
    }
  },
  async mounted() {
    await this.getPhotoDoc()
    this.anno = await new Annotorious({ image: this.photo.filename })
  },
  methods: {
    async getPhotoDoc() {
      const docRef = await this.$fireStore
        .collection('photos')
        .doc(this.$route.params.id)
        .get()
      try {
        if (docRef.exists) {
          // data() returns all data about the doc
          console.log('got the doc: ', docRef.data())
          this.photo = docRef.data()
        } else {
          console.log('no docs exist')
        }
      } catch (error) {
        console.log('Error getting document:', error)
      }
      //       try {
      //   this.$fireStore.collection('photos').onSnapshot((snapShot) => {
      //     const snapData = []
      //     snapShot.forEach((doc) => {
      //       snapData.push(doc.data())
      //     })
      //     this.photos = []
      //     this.photos = snapData
      //   })
      // } catch (error) {
      //   console.log('Error getting all docs: ', error)
      // }
      // try {
      //   await this.$bind('photo', db.collection('photos').doc(this.$route.params.id))
      // } catch (error) {
      //   console.log('Error getting document:', error)
      // }
    }
  },
  head() {
    return {
      title: this.photo.title,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: this.photo.description
        }
      ]
    }
  }
}
</script>
