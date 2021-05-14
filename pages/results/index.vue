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
              <b-button>CSV</b-button>
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
