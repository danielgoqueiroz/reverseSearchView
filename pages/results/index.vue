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
                >CSV</b-button>
              <b-button @click="remove(result.hash)">APAGAR</b-button>
              <b-button>Detalhes</b-button>
            </b-button-group>
          </b-col>
        </b-row>
      </b-list-group-item>
    </b-list-group>
  </b-container>
</template>

<script lang="ts">
import Vue from 'vue'
const crypto = require('crypto')

export default Vue.extend({
  name: 'Search',
  data() {
    return {
      results: [],
      loading: false,
    }
  },
  computed: {},
  mounted() {
    this.getResults()
  },
  methods: {
    remove(hash: string) {

    },
    jsontoCsv(json: string) {

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
