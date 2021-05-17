<template>
  <b-container fluid class="content">
    <b-row cols="3">
      <b-col></b-col>
      <b-col
        ><b-img
          thumbnail
          fluid
          rounded
          :v-if="validateLink(link) != ''"
          class="image-preview"
          height="250px"
          :src="validateLink(link)"
      /></b-col>
      <b-col></b-col>
    </b-row>

    <b-input-group class="pt-5">
      <b-input-group-prepend>
        <b-input-group-text> Url </b-input-group-text>
      </b-input-group-prepend>
      <b-form-input
        v-model="link"
        placeholder="Digite o endereço da imagem"
      ></b-form-input>
      <b-input-group-append>
        <b-overlay :show="loading">
          <b-button
            :varian="isOn ? 'success' : 'secondary'"
            @click="search(link)"
            >Buscar</b-button
          >
        </b-overlay>
      </b-input-group-append>
      <b-input-group-append>
        <b-button
          :disabled="resultFinded.length === 0"
          :varian="isOn ? 'success' : 'secondary'"
          @click="downloadCSV()"
          >CSV</b-button
        >
      </b-input-group-append>
    </b-input-group>

    <b-col>
      <Result :results="resultFinded" />
    </b-col>
  </b-container>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'Search',
  data() {
    return {
      // link: 'https://img.r7.com/images/bolsonaro-passeia-de-moto-com-centenas-de-apoiadores-09052021134454757?dimensions=442x241',
      link: '',
      isOn: false,
      resultFinded: [],
      loading: false,
    }
  },
  computed: {},
  mounted() {
    this.isServiceOn()
  },
  methods: {
    validateLink(link: string): any {
      try {
        const url = new URL(link)
        this.$axios
          .get(link)
          .then((result: any) => {
            if (result) {
              return link
            }
          })
          .catch(() => {
            return ''
          })
        return url.toString()
      } catch (err) {
        return ''
      }
    },
    downloadCSV() {
      console.log('csv')
    },
    getResult(res: Array<Object>): any {
      if (res === undefined || res === null) {
        return []
      }
      return res
    },
    async search(url: string) {
      this.loading = true
      try {
        const link = new URL(url)
        // this.loading = true
        await this.$axios
          .get(`http://phantomcode.ddns.net/reverseSearch/search?url=${link}`)
          .then((result) => {
            // this.loading = true
            this.resultFinded = result.data.results
          })
          .catch(() => {
            this.isOn = false
          })
        this.loading = false
      } catch (err) {
        this.link = ''
        this.loading = false
      }
    },
    isServiceOn() {
      this.$axios
        .get('http://phantomcode.ddns.net/')
        .then((result) => {
          this.isOn = result.status === 200
        })
        .catch(() => {
          this.isOn = false
        })
    },
  },
})
</script>

<style>
.image-preview {
  text-align: center;
}
.content {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 50px;
}
</style>
