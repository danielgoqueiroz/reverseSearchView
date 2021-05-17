<template>
  <b-container>
    <b-list-group>
      <b-list-group-item
        v-for="key in Object.getOwnPropertyNames(resultAgrouped)"
        :key="key.index"
        class="flex-column align-items-start"
      >
        <div class="d-flex w-100 justify-content-between">
          <h5 class="mb-1">{{ key }}</h5>
          <medium class="text-muted"
            ><b-badge>{{ resultAgrouped[key].length }} </b-badge>
          </medium>
        </div>
        <b-list-group-item v-for="item in resultAgrouped[key]" :key="item.link">
          <b-row>
            <b-col col lg="2"><b-img :src="item.preview" /></b-col>
            <b-col cols="10"
              ><a class="link" :href="item.link" target="_blank">{{
                item.link
              }}</a>
              <br
            /></b-col>
          </b-row>
        </b-list-group-item>
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
  computed: {
    resultAgrouped(): any {
      const results = this.groupBy(this.results, 'host')
      return results
    },
  },
  methods: {
    groupBy(list: any, key: string) {
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
