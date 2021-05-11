<template>
  <b-container>
    <b-input-group prepend="Url" class="pt-5">
      <b-form-input
        v-model="link"
        placeholder="Digite o endereço da imagem"
      ></b-form-input>
      <b-input-group-append>
        <b-button :varian="isOn ? 'success' : 'secondary'" @click="search(link)"
          >Buscar</b-button
        >
      </b-input-group-append>
    </b-input-group>

    <b-list-group>
      <b-list-group-item
        v-for="result in resultFinded"
        :key="result.index"
        class="flex-column align-items-start"
      >
        <div class="d-flex w-100 justify-content-between">
          <h5 class="mb-1">{{ result.host }}</h5>
          <small class="text-muted"></small>
        </div>
        <a :href="result.link">{{ result.link }}</a>

        <small class="text-muted">{{ result.text }}</small>
      </b-list-group-item>
    </b-list-group>
  </b-container>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  data() {
    return {
      // link: 'https://amazonas.news/wp-content/uploads/2021/04/image.jpg',
      link: '',
      isOn: false,
      resultFinded: [
        // {
        //   host: 'www.youtube.com',
        //   link: 'https://www.youtube.com/watch?v=0dIDLWt1fo0',
        //   text: '1 de jan. de 2021 — O presidente Jair Bolsonaro (sem partido) nadou com banhistas de Praia Grande (SP), causando enorme aglomeração na água, nesta ...',
        // },
        // {
        //   host: 'www1.folha.uol.com.br',
        //   link: 'https://www1.folha.uol.com.br/poder/2021/01/bolsonaro-repete-tour-na-praia-grande-em-sp-e-ignora-pandemia-da-covid-em-contato-com-banhistas.shtml',
        //   text: '4 de jan. de 2021 — Gilmar Alves Jr. Praia Grande (SP). O presidente Jair Bolsonaro (sem partido) repetiu nesta segunda-feira (4), último ...',
        // },
        // {
        //   host: 'revistaforum.com.br',
        //   link: 'https://revistaforum.com.br/brasil/bolsonaro-se-despede-do-litoral-de-sp-com-nova-aglomeracao-em-praia-grande/',
        //   text: '780 × 640 · 4 de jan. de 2021 — O presidente resolveu fazer um passeio na praia do Canto do Forte, em Praia Grande. Como sempre, estava sem máscara e ignorou, novamente ...',
        // },
        // {
        //   host: 'www.santaportal.com.br',
        //   link: 'https://www.santaportal.com.br/noticia/68793-jair-bolsonaro-e-visto-andando-de-jet-ski-e-conversando-com-eleitores-em-praia-grande',
        //   text: '780 × 640 · 4 de jan. de 2021 — Durante a semana, Bolsonaro também aproveitou para fazer passeios pela região. Na sexta-feira (1º), ele passeou de lancha em Praia Grande e ...',
        // },
        // {
        //   host: 'costanorte.com.br',
        //   link: 'https://costanorte.com.br/nacional/brasil-est%C3%A1-quebrado-e-eu-n%C3%A3o-consigo-fazer-nada-diz-bolsonaro-contrariando-otimismo-de-guedes-1.263598',
        //   text: '1000 × 500 · 5 de jan. de 2021 — Bolsonaro em férias na Baixada Santita / Foto: Reprodução / Praia Grande Mil Grau. O presidente Jair Bolsonaro declarou a um grupo de ...',
        // },
      ],
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
