<template>
  <b-container>
    <b-list-group>
      <b-list-group-item
        v-for="key in Object.getOwnPropertyNames(resultAgrouped(results))"
        :key="key.index"
        class="flex-column align-items-start"
      >
        <div class="d-flex w-100 justify-content-between">
          <h5 class="mb-1">{{ key }}</h5>
          <small class="text-muted"
            ><b-badge>{{ resultAgrouped(results)[key].length }} </b-badge>
          </small>
        </div>
        <b-list-group>
          <b-list-group-item
            v-for="result in resultAgrouped(results)[key]"
            :key="result.link"
          >
            <b-img fluid :src="result.preview"></b-img>
            <b-link small :href="result.link" target="_blank">{{
              result.link
            }}</b-link>
          </b-list-group-item>
        </b-list-group>
      </b-list-group-item>
    </b-list-group>
  </b-container>
</template>

<script lang="ts">
// interface Result {
//   host: string
//   link: string
//   text: string
//   preview: string
// }

export default {
  name: 'Result',
  props: ['results'],
  methods: {
    resultAgrouped(results: any): any {
      return this.groupBy(results, 'host')
    },
    groupBy(list: any, key: string): any {
      return list.reduce(function (rv: any, x: any) {
        ;(rv[x[key]] = rv[x[key]] || []).push(x)
        return rv
      }, {})
    },
  },
}
</script>

<style>
.link {
  font-size: 10px;
}
</style>
