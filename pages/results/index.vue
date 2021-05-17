<template>
  <b-container flex>
    <b-list-group>
      <b-list-group-item v-for="result in results" :key="result.index">
        <b-row>
          <b-col>
            <b-img :src="result.link" width="120px"></b-img>
          </b-col>
          <b-col>
            <b-button-group>
              <b-button
                :href="getCsvLink(result.link)"
                :alt="getCsvLink(result.link)"
                >CSV</b-button
              >
              <b-button @click="remove(result.hash)">APAGAR</b-button>
              <b-button @click="showModal(result)"> Detalhes </b-button>
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
    }
  },
  computed: {},
  mounted() {
    this.getResults()
  },
  methods: {
    showModal(results: any) {
      console.log(results)
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
        .delete(`http://phantomcode.ddns.net/reverseSearch/result?hash=${hash}`)
        .then((result) => {
          if (result.status === 201) {
            this.getResults()
            console.log('Item removido')
          }
        })
        .catch((err) => {
          console.log('Erro ao remover: ' + err)
        })
    },
    jsontoCsv(json: string) {
      console.log(json)
      // let csvContent = "";
      // json.forEach((item) => {
      //   let line = `${item.host};${item.link};${item.text}\n`;
      //   csvContent += line;
      // });
      // fs.writeFile(csvPath, csvContent, "utf-8", function (err) {
      //   if (err) throw err;
      //   console.log(`Json salvo: ${csvPath}`);
      // });
    },
    getHash(text: string): string {
      return crypto.createHash('md5').update(text).digest('hex')
    },
    getCsvLink(link: string) {
      return `http://phantomcode.ddns.net/api/resources/csv/${this.getHash(
        link
      )}.csv`
    },
    getResults() {
      this.$axios
        .get('http://phantomcode.ddns.net/reverseSearch/results')
        .then((result) => {
          this.results = result.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
  },
})
</script>

<style></style>
