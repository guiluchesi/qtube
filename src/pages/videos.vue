<template>
  <q-page>
    <q-page-container>
      <q-ajax-bar ref="bar"/>
      <div class="row" v-if="checkDatabase">
        <div class="col-md-3 row justify-center q-mb-md" :key="index" v-for="(info, index) of infos">
          <q-card inline class="col-md-10" color="purple">
            <q-card-media>
              <img :src="`${appFolder}${info.thumbnail}`">
            </q-card-media>
            <q-card-title class="relative-position">
              <div class="ellipsis q-caption" :title="info.title">
                <q-tooltip>{{info.title}}</q-tooltip>
                {{info.title}}
              </div>
              <div slot="right" class="row items-center">
                <q-icon name="remove_red_eye" />
              </div>
            </q-card-title>
            <q-card-separator />
            <q-card-actions>
              <q-btn @click="goTo(info)">
                <q-icon class="q-mr-sm" name="play_circle_filled">
                </q-icon>
                VIEW
              </q-btn>
            </q-card-actions>
          </q-card>
        </div>
      </div>
      <div class="row" v-else>
        <div class="col-md-12 row justify-center q-mb-md">
          <q-card inline class="col-md-10" color="purple">
            <h2>Don't find any videos in your app!!</h2>
          </q-card>
        </div>
      </div>
    </q-page-container>
  </q-page>
</template>

<script>
import { ipcRenderer } from 'electron'

export default {
  name: 'VideoPage',
  data () {
    return {
      infos: [],
      checkDatabase: false,
      appFolder: ipcRenderer.sendSync('getFolderApp')
    }
  },
  mounted () {
    this.listVideos()
  },
  methods: {
    listVideos () {
      this.$refs.bar.start()
      this.$axios.get('http://localhost:3000/api/infos')
        .then(data => {
          this.infos = data.data.videos
          if (this.infos.length) this.checkDatabase = true
          else this.checkDatabase = false
          this.$refs.bar.stop()
        })
        .catch(err => {
          this.$refs.bar.stop()
          console.log(err)
        })
    },
    goTo (info) {
      this.videoSelect(info)
      this.$router.push({ name: 'player', params: { src: info.src, img: info.thumbnail } })
    },
    videoSelect (info) {
      window.localStorage.setItem('media', JSON.stringify(info))
    }
  }
}
</script>

<style>
</style>
