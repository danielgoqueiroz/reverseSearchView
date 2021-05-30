<template>
  <b-container fluid class="content">
    <b-container>
      <b-row cols="3" class="search">
        <b-col></b-col>
        <b-col
          ><b-img
            thumbnail
            fluid
            rounded
            :v-show="validateLink(link) == ''"
            :v-if="validateLink(link) != ''"
            class="image-preview"
            height="250px"
            :src="validateLink(link)"
        /></b-col>
        <b-col></b-col>
      </b-row>
    </b-container>
    <br />

    <b-container>
      <b-input-group>
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
              variant="primary"
              :varian="isOn ? 'success' : 'secondary'"
              :disabled="validateLink(link) == '' && !isEmailValid(email)"
              @click="search(link, email)"
              ><b-icon-search
            /></b-button>
          </b-overlay>
        </b-input-group-append>
      </b-input-group>
    </b-container>

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
      link: '',
      isOn: false,
      resultFinded: [],
      loading: false,
    }
  },
  computed: {
    email(): any {
      const email = this.$auth.$storage.getLocalStorage('email')
      return email !== undefined && email !== null ? email : ''
    },
    emailAction(): any {
      const mail = this.$store.state.user.email
      return localStorage.setItem('email', JSON.stringify(mail))
    },
  },
  mounted() {
    this.isServiceOn()
    const email = localStorage.getItem('email')
    if (email === undefined || email === null) {
      this.email = ''
    }
  },
  methods: {
    isEmailValid(email: string) {
      if (email === undefined || email === null || email.length < 5) {
        return false
      }
      return /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/.test(
        email
      )
    },
    downloadCsv(csvContent: any) {
      this.$axios
        .get('', { responseType: 'blob' })
        .then(() => {
          const blob = new Blob([csvContent], { type: 'text/csv' })
          const link = document.createElement('a')
          link.href = URL.createObjectURL(blob)
          link.download = `${name}.csv`
          link.click()
          URL.revokeObjectURL(link.href)
        })
        .catch()
    },
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
          .get(
            `http://phantomcode.ddns.net/reverseSearch/search?url=${link}&email=${this.email}`
          )
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
.email {
  width: 400px;
}
</style>
