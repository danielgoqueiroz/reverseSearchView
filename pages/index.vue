<template>
  <b-container>
    <b-row>
      <b-col>
        <div class="container">
          <b-input-group prepend="Url">
            <b-form-input
              v-model="link"
              placeholder="Digite o endereço da imagem"
            ></b-form-input>
            <b-input-group-append>
              <b-button
                :varian="isOn ? 'success' : 'secondary'"
                @click="search(link)"
                >Buscar</b-button
              >
            </b-input-group-append>
          </b-input-group>
        </div>
      </b-col>
    </b-row>
    <b-row>
      <!-- <b-table striped hover :items="getResult(resultFinded)"></b-table> -->
      <b-col> {{ getResult(resultFinded) }} </b-col>
    </b-row>
  </b-container>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  data() {
    return {
      link: 'https://amazonas.news/wp-content/uploads/2021/04/image.jpg',
      isOn: false,
      teste: {},
      resultFinded: {},
    }
  },
  mounted() {
    this.isServiceOn()
  },
  methods: {
    getResult(res: Array<Object>): any {
      if (res === undefined || res === null) {
        return []
      }
      return res
    },
    search(url: String) {
      this.$axios
        .get(`http://phantomcode.ddns.net/reverseSearch?url=${url}`)
        .then((result) => {
          this.resultFinded = result.data
        })
        .catch(() => {
          this.isOn = false
        })
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

<style></style>
