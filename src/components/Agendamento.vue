<template>
  <v-container>
    <v-row class="text-center">

      <v-col class="mb-4">
        <h1 class="display-1 font-weight-bold mb-3">
          Agendamento de Consulta
        </h1>

        <p class="subheading font-weight-regular">
          Aqui pode poder realizar o agendamento de suas consultas,
          <br>por favor preencha os campos abaixo
        </p>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  export default {
    name: 'AgendarConsulta',

    created() {
      this.$http.post(`/specialties/list`)
      .then(res => (this.specialties = res.data))

    },
    data() {
      return {
        specialties: null
      }
    }
  }
</script>
