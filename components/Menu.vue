<template>
  <b-container class="menu centered">
    <b-card>
      <b-container class="email">
        <b-input-group>
          <b-input-group-prepend>
            <b-input-group-text>Email</b-input-group-text>
          </b-input-group-prepend>
          <b-form-input
            v-model="emailForm"
            placeholder="Informe um e-mail válido"
          ></b-form-input>
          <b-input-group-append>
            <b-button
              :variant="isEmailValid() ? 'success' : 'danger'"
              :disabled="email !== null"
              @click="search(link, email)"
            >
              <b-icon-check v-if="isEmailValid()" />
              <b-icon-x v-else />
            </b-button>
          </b-input-group-append>
        </b-input-group>
      </b-container>
      <br />

      <b-container v-show="isEmailValid()">
        <b-button-group>
          <b-button variant="secondary" to="/search"
            ><b-icon-search /> Buscar
          </b-button>
          <b-button variant="secondary" to="/results"
            ><b-icon-files /> Resultados</b-button
          >
        </b-button-group>
      </b-container>
    </b-card>
    <br />
  </b-container>
</template>

<script>
export default {
  data() {
    return {
      emailForm: this.$auth.$storage.getLocalStorage('email'),
    }
  },
  computed: {
    email() {
      return this.$auth.$storage.setLocalStorage('email', this.emailForm)
    },
  },
  mounted() {},
  methods: {
    isEmailValid() {
      if (
        this.email === undefined ||
        this.email === null ||
        this.email.length < 5
      ) {
        return false
      }
      return /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/.test(
        this.email
      )
    },
  },
}
</script>

<style>
.menu {
  position: relative;
  overflow: hidden;
}
.centered {
  float: none;
  position: relative;
  text-align: center;
  margin-bottom: 25px;
}
</style>
