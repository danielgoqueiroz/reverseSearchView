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
              <b-button>APAGAR</b-button>
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
