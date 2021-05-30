/* eslint-disable no-console */ /* eslint-disable no-console */ /*
eslint-disable no-console */
<template>
  <b-container flex>
    <b-list-group>
      <b-list-group-item v-for="result in results" :key="result.index">
        <b-row>
          <b-col>
            <b-img :src="result.link" width="80px"></b-img>
          </b-col>
          <b-col>
            <b-button-group>
              <b-button variant="info" @click="downlodCsv(result)"
                ><b-icon-arrow-down
              /></b-button>
              <!-- <b-button variant="success" @click="sendCsvToEmail(result)"
                ><b-icon-cloud-arrow-up-fill
              /></b-button> -->
              <b-button variant="primary" @click="showModal(result)">
                <b-icon-info-circle />
              </b-button>
              <b-button variant="danger" @click="remove(result.hash)"
                ><b-icon-trash
              /></b-button>
            </b-button-group>
            <b-modal v-model="modalShow" title="Resultados">
              <Result :results="resultModal" />
            </b-modal>
          </b-col>
        </b-row>
      </b-list-group-item>
    </b-list-group>
  </b-container>
</template>

<script lang="ts">
import Vue from 'vue'
import Result from '~/components/Result.vue'
const crypto = require('crypto')

export default Vue.extend({
  name: 'Search',
  components: { Result },
  data() {
    return {
      results: [],
      resultModal: [],
      modalShow: false,
      loading: false,
      // email: 'pubdaniel@gmail.com',
    }
  },
  computed: {
    email(): any {
      const mail = this.$auth.$storage.getLocalStorage('email')
      return mail === undefined
        ? ''
        : this.$auth.$storage.getLocalStorage('email')
    },
  },
  mounted() {
    this.getResults()
  },
  methods: {
    showModal(results: any) {
      this.resultModal = results.results
      this.modalShow = true
    },
    groupBy(list: any, key: any) {
      return list.reduce(function (rv: any, x: any) {
        ;(rv[x[key]] = rv[x[key]] || []).push(x)
        return rv
      }, {})
    },
    remove(hash: string) {
      this.$axios
        .delete(
          `http://phantomcode.ddns.net/reverseSearch/result?email=${this.email}&hash=${hash}`
        )
        .then((result) => {
          if (result.status === 201) {
            this.getResults()
            console.info('Item removido')
          }
        })
        .catch((err) => {
          console.error('Erro ao remover: ' + err)
        })
    },
    sortByProperty(property: string) {
      return function (a: any, b: any) {
        if (a[property] > b[property]) return 1
        else if (a[property] < b[property]) return -1
        return 0
      }
    },
    jsontoCsv(name: string, json: any) {
      const orderedItens = json.sort(this.sortByProperty('link'))

      let csvContent = 'Site,Link, \r\n'

      orderedItens.forEach((item: any) => {
        const line = `${item.host},${item.link}\r\n`
        csvContent += line
      })
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
        .catch(() => {
          console.error('Erro ao baixar aquivo')
        })
    },
    getHash(text: string): string {
      return crypto.createHash('md5').update(text).digest('hex')
    },
    async sendCsvToEmail(result: any) {
      console.info(`Enviando csv ${result.hash}.csv para ${result.email}.`)
      await this.$axios.get(
        `http://phantomcode.ddns.net/reverseSearch/result/send?email=${this.email}&hash=${result.hash}`
      )
    },
    downlodCsv(result: any) {
      this.jsontoCsv(result.hash, result.results)
    },
    getResults(): any {
      if (this.email === '') {
        this.results = []
      } else {
        this.$axios
          .get(
            `http://phantomcode.ddns.net/reverseSearch/results?email=${this.email}`
          )
          .then((result) => {
            this.results = result.data
          })
          .catch((err) => {
            console.log(err)
          })
      }
    },
  },
})
</script>

<style></style>
