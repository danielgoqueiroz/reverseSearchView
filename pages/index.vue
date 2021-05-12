<template>
  <b-container>
    <b-input-group class="pt-5">
      <b-input-group-prepend>
        <b-input-group-text>
          <b-img
            height="20px"
            v-if="link !== undefined && link.length > 0"
            :src="link"
          />
          <div v-else>Url</div>
        </b-input-group-text>
      </b-input-group-prepend>
      <b-form-input
        v-model="link"
        placeholder="Digite o endereço da imagem"
      ></b-form-input>
      <b-input-group-append>
        <b-button :varian="isOn ? 'success' : 'secondary'" @click="search(link)"
          >Buscar</b-button
        >
      </b-input-group-append>
      <!-- <b-input-group-append>
        <b-button
          :varian="isOn ? 'success' : 'secondary'"
          @click="downloadResult()"
          >Baixar CSV</b-button
        >
      </b-input-group-append> -->
    </b-input-group>

    <b-list-group>
      <b-list-group-item
        v-for="key in Object.getOwnPropertyNames(resultAgrouped)"
        :key="key.index"
        class="flex-column align-items-start"
      >
        <div class="d-flex w-100 justify-content-between">
          <h5 class="mb-1">{{ key }}</h5>
          <medium class="text-muted"
            ><b-badge>{{ resultAgrouped[key].length }} </b-badge></medium
          >
        </div>
        <!-- {{ resultAgrouped[key] }} -->
        <b-list-group-item v-for="item in resultAgrouped[key]" :key="item.link">
          <b-row>
            <b-col col lg="2"><b-img :src="item.preview" /></b-col>
            <b-col cols="10"
              ><a :href="item.link">{{ item.link }}</a> <br
            /></b-col>
          </b-row>
        </b-list-group-item>

        <!-- <small class="text-muted">{{ result.index }}</small> -->
      </b-list-group-item>
    </b-list-group>
  </b-container>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'App',
  data() {
    return {
      // link: 'https://img.r7.com/images/bolsonaro-passeia-de-moto-com-centenas-de-apoiadores-09052021134454757?dimensions=442x241',
      link: '',
      isOn: false,
      resultFinded: [],
    }
  },
  computed: {
    resultAgrouped(): any {
      const results = this.groupBy(this.resultFinded, 'host')
      return results
    },
  },
  mounted() {
    this.isServiceOn()
  },
  methods: {
    groupBy(list: any, key: any) {
      return list.reduce(function (rv: any, x: any) {
        ;(rv[x[key]] = rv[x[key]] || []).push(x)
        return rv
      }, {})
    },
    getResult(res: Array<Object>): any {
      if (res === undefined || res === null) {
        return []
      }
      return res
    },
    search(url: string) {
      try {
        const link = new URL(url)
        this.$axios
          .get(`http://phantomcode.ddns.net/reverseSearch?url=${link}`)
          .then((result) => {
            // const resultGrouped = _.groupBy(result.data.results, (r) => {
            //   return r.host
            // })

            this.resultFinded = result.data.results
          })
          .catch(() => {
            this.isOn = false
          })
      } catch (err) {
        this.link = ''
        console.log('O link parece inválido')
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

<style></style>
