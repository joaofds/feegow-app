<template>
  <v-container>
    <v-row class="text-center">
      <v-col class="mb-4">
        <h1 class="display-1 font-weight-bold mb-3">Agendamento de Consulta</h1>

        <p class="subheading font-weight-regular">
          Aqui você pode realizar o agendamento de consultas.<br />
        </p>
      </v-col>
    </v-row>

    <v-row class="d-flex justify-center">
      <v-col cols="4">
        <div v-if="specialties.length">
          <v-select
            :items="specialties"
            label="Selecione a especialidade..."
            item-text="nome"
            item-value="especialidade_id"
            v-model="selects.specialtie">
          </v-select>
          <div class="d-flex justify-end">
            <v-btn @click="searchProfessioanls()" small color="success" dark>
              Buscar
            </v-btn>
          </div>
        </div>
        <p v-else class="d-flex justify-center subheading font-weight-regular">
          Carregando dados...
        </p>
      </v-col>
    </v-row>

    <v-overlay :value="overlay">
      <v-progress-circular indeterminate size="64"></v-progress-circular>
    </v-overlay>
  </v-container>
</template>

<script>
export default {
  name: "AgendarConsulta",

  mounted() {
    this.overlay = true;
    this.$http
      .post(`/specialties/list`)
      .then((res) => {
        this.specialties = res.data.content;
      })
      .then(() => {
        this.overlay = false;
      });
  },
  data() {
    return {
      overlay: false,
      specialties: [],
      selects: {
        specialtie: null
      }
    };
  },
  methods: {
    searchProfessioanls() {
      let id = this.selects.specialtie;
      //this.overlay = true;
      this.$http
        .post(`/professional/list`, {
          params: {
            id: id
          }
        })
        .then((res) => {
          console.log(res.data)
        });
    },
  },
  computed: {

  },
};
</script>

<style scoped>
.v-list-item__title {
  text-transform: uppercase;
}
</style>
