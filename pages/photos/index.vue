<template>
  <section>
    <h1 class="h5">Latest Mystery Photos</h1>
    <aside class="text-right mb-3">
      <b-form-group>
        <b-form-radio-group
          id="btn-status"
          v-model="selected"
          :options="options"
          buttons
          button-variant="outline-secondary"
          name="radio-btn-outline"
          @change="onSort"
        ></b-form-radio-group>
      </b-form-group>
    </aside>
    <spinner v-if="isLoading">Loading...</spinner>
    <div v-if="!isLoading" class="card-columns">
      <div v-for="photo in clientPhotos" :key="photo.id" class="card">
        <b-card-img-lazy
          blank
          blank-color="#bbb"
          :src="photo.url"
          class="card-img-top"
          :alt="photo.caption"
        ></b-card-img-lazy>
        <div class="card-body">
          <h5 class="card-title">{{ photo.title }}</h5>
          <!-- <button
            id="deleteButton"
            @click="deletePhoto({ docId: photo.id, photoId: photo.filename })"
          >
            Delete
          </button> -->
          <div class="card-text">
            {{ photo.description }}
            <nuxt-link
              :to="{
                name: 'photos-id-title',
                params: { id: photo.id, title: photo.title }
              }"
            >
              <button
                v-if="photo.status == 'Solved'"
                type="button"
                class="btn btn-success btn-sm stretched-link"
              >
                Status
                <span class="badge badge-light pt-1">{{ photo.status }}</span>
              </button>
              <button
                v-if="photo.status == 'Unsolved'"
                type="button"
                class="btn btn-danger btn-sm stretched-link"
              >
                Status
                <span class="badge badge-light pt-1">{{ photo.status }}</span>
              </button>
            </nuxt-link>
            <br />
            <section class="py-2">
              <b-icon-check2-square></b-icon-check2-square>
              <span class="badge badge-light mr-1">2</span>
              <svg
                width="1em"
                height="1em"
                viewBox="0 0 16 16"
                class="bi bi-chat-left text-muted"
                fill="currentColor"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  fill-rule="evenodd"
                  d="M14 1H2a1 1 0 0 0-1 1v11.586l2-2A2 2 0 0 1 4.414 11H14a1 1 0 0 0 1-1V2a1 1 0 0 0-1-1zM2 0a2 2 0 0 0-2 2v12.793a.5.5 0 0 0 .854.353l2.853-2.853A1 1 0 0 1 4.414 12H14a2 2 0 0 0 2-2V2a2 2 0 0 0-2-2H2z"
                />
              </svg>
              <span class="badge badge-light ml-1">4</span>
              <b-icon-bounding-box-circles
                v-b-popover.hover.top="'Number of image annotations'"
                class="text-muted"
              ></b-icon-bounding-box-circles>
              <span class="badge badge-light">25</span>
            </section>
            <div class="small text-muted">
              asked {{ photo.createdAt.toDate().toDateString() }} by
              {{ photo.username }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'PhotosList',
  // For SEO
  async asyncData({ app, error }) {
    const photosArr = []
    const docRef = await app.$fireStore.collection('photos').get()
    try {
      docRef.forEach((doc) => photosArr.push(doc.data()))
      return {
        photos: photosArr
      }
    } catch (e) {
      error({
        statusCode: 503,
        message: 'Unable to fetch posts at this time. Please try again.'
      })
    }
  },
  data() {
    return {
      clientPhotos: [],
      isLoading: false,
      selected: 'all',
      options: [
        { text: 'All', value: 'all' },
        { text: 'Unsolved', value: 'unsolved' },
        { text: 'Solved', value: 'solved' }
      ]
    }
  },
  mounted() {
    this.allClientPhotos()
  },
  methods: {
    onSort(evt) {
      if (evt === 'solved') {
        this.solvedPhotos()
      } else if (evt === 'unsolved') {
        this.unSolvedPhotos()
      } else {
        this.allClientPhotos()
      }
    },
    async allClientPhotos() {
      try {
        this.isLoading = true
        await this.$bind(
          'clientPhotos',
          this.$fireStore.collection('photos').orderBy('createdAt', 'desc')
        )
        this.isLoading = false
      } catch (error) {
        console.log('problem sorting all photos: ', error)
      }
    },
    async solvedPhotos() {
      try {
        this.isLoading = true
        // becomes a composite index
        await this.$bind(
          'clientPhotos',
          this.$fireStore
            .collection('photos')
            .where('status', '==', 'Solved')
            .orderBy('createdAt', 'desc')
        )
        this.isLoading = false
      } catch (error) {
        console.log('problem sorting solved photos: ', error)
      }
    },
    async unSolvedPhotos() {
      try {
        this.isLoading = true
        await this.$bind(
          'clientPhotos',
          this.$fireStore
            .collection('photos')
            .where('status', '==', 'Unsolved')
            .orderBy('createdAt', 'desc')
        )
        this.isLoading = false
      } catch (error) {
        console.log('Error getting unsolved docs: ', error)
      }
    },
    async deletePhoto(payload) {
      try {
        const photoRef = await this.$fireStorage.ref(
          'photos/' + payload.photoId
        )
        const docRef = await this.$fireStore
          .collection('photos')
          .doc(payload.docId)
        photoRef.delete().then(() => console.log('photo deleted'))
        docRef.delete().then(() => console.log('doc deleted'))
      } catch (error) {
        console.log(error)
      }
    }
  },
  head() {
    return {
      title: 'Mystery Photos',
      meta: [
        // hid is used as unique identifier. Do not use `vmid` for it as it will not work
        {
          hid: 'description',
          name: 'description',
          content: 'My custom description about this page here'
        }
      ]
    }
  }
}
</script>
