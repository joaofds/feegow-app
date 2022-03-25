<template>
  <v-container>
    <!-- inicio especialidades -->
    <v-row class="text-center">
      <v-col class="mb-4">
        <h1 class="display-1 font-weight-bold mb-3">Agendamento de Consulta</h1>
        <p class="subheading font-weight-regular">
          Aqui você pode realizar o agendamento de consultas.<br />
        </p>
      </v-col>
    </v-row>
    <v-row class="d-flex justify-center">
      <v-col cols="8">
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
    <!-- fim especialidades -->
    
    <!-- inicio cards profissionais -->
    <v-row class="d-flex justify-center">
      <v-col cols="8">
        <v-simple-table>
          <template v-slot:default>
            <thead>
              <tr>
                <th class="text-left">#</th>
                <th class="text-left">NOME</th>
                <th class="text-left">CRM</th>
                <th class="text-left">AÇÃO</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(p, index) in professionals" :key="index">
                <td>{{ index + 1 }}</td>
                <td>{{ p.tratamento }} {{ p.nome }}</td>
                <td>{{ p.documento_conselho ? p.documento_conselho : 'N/A' }}</td>
                <td>
                  <v-btn @click.stop="getTableDataRow(p, index)" x-small rounded color="success mr-3">
                    Agendar
                  </v-btn>
                </td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-col>
    </v-row>
    <!-- fim cards profissionais -->

    <!-- inicio overlay load -->
    <v-overlay :value="widgets.overlay">
      <v-progress-circular indeterminate size="64"></v-progress-circular>
    </v-overlay>
    <!-- fim overlay load -->

    <!-- inicio snackbar notification -->
    <v-snackbar
      v-model="widgets.snackbar"
      :timeout="widgets.timeout"
      right top>
      {{ widgets.text }}

      <template v-slot:action="{ attrs }">
        <v-btn
          color="#fff"
          text
          v-bind="attrs"
          @click="widgets.snackbar = false"
        >
          Fechar
        </v-btn>
      </template>
    </v-snackbar>
    <!-- fim snackbar notification -->

    <!-- inicio modal -->
    <v-row justify="center">
      <v-dialog
        v-model="widgets.dialog"
        persistent
        max-width="600px"
      >
        <v-card>
          <v-card-title>
            <span class="text-h5">User Profile</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-form ref="formModal" @submit.prevent="checkForm">
                <v-row>
                    <v-col cols="12" sm="12" md="6">
                      <v-text-field label="Nome*" required :rules="rules" v-model="name">
                      </v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="6">
                      <v-select
                          :items="how"
                          label="Como conheceu?*"
                          item-text="nome_origem"
                          item-value="origem_id"
                          v-model="selects.how"
                          required :rules="rules">
                      </v-select>
                    </v-col>
                    <v-col cols="12" sm="12" md="6">
                      <v-text-field type="text" label="CPF*" required maxlength="11"
                      v-model="cpf" :rules="cpfRules">
                      </v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="6">
                      <v-menu
                        ref="menu"
                        v-model="menu"
                        :close-on-content-click="false"
                        transition="scale-transition"
                        offset-y
                        min-width="auto"
                      >
                        <template v-slot:activator="{ on, attrs }">
                          <v-text-field
                            v-model="date"
                            label="Nascimento"
                            prepend-icon="mdi-calendar"
                            readonly
                            v-bind="attrs"
                            v-on="on"
                            :rules="rules"
                          ></v-text-field>
                        </template>
                        <v-date-picker
                          v-model="date"
                          :active-picker.sync="activePicker"
                          :max="(new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10)"
                          min="1950-01-01"
                          @change="saveNewDate"
                        ></v-date-picker>
                      </v-menu>
                    </v-col>
                </v-row>
                <small>Todos os campos são obrigatórios</small>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn small color="grey lighten-1" dark @click="widgets.dialog = false">
                    Cancelar
                  </v-btn>
                  <v-btn type="submit" small color="success" dark>
                    Solocitar Horários
                  </v-btn>
                </v-card-actions>
              </v-form>
            </v-container>
          </v-card-text>
        </v-card>
        <!-- inicio loading dialog 2 -->
        <v-dialog v-model="dialog" hide-overlay
          persistent
          width="300"
        >
          <v-card color="primary" dark>
            <v-card-text>
              Enviando solicitação...
              <v-progress-linear
                indeterminate
                color="white"
                class="mb-0"
              ></v-progress-linear>
            </v-card-text>
          </v-card>
        </v-dialog>
        <!-- fim loading dialog 2 -->
      </v-dialog>
    </v-row>
    <!-- fim modal -->
  </v-container>
</template>

<script>

export default {
  name: "AgendarConsulta",

  mounted() {

    // busca especialidades
    this.widgets.overlay = true
    this.$http
      .post(`/specialties/list`)
      .then((res) => {
        this.specialties = res.data.content;
      })
      .catch((err) => {
        console.log(err)
        this.widgets.overlay = true
        
      });
    
    
    // busca listagem de "comomo conheceu?"
    this.$http
      .post(`/patient/list-sources`)
      .then((res) => {
        this.how = res.data.content
      })
      .then(() => {
        this.widgets.overlay = false
      })
      .catch((err) => {
        console.log(err)
        this.widgets.overlay = true
      });
  },
  watch: {
    menu(menu) {
      menu && setTimeout(() => (this.activePicker = 'YEAR'))
    },
  },
  data() {
    return {
      specialties: [],
      professionals: [],
      tempProfId: null,
      how: [],
      selects: {
        specialtie: null,
        how: null
      },
      widgets: {
        overlay: false,
        snackbar: false,
        text: '',
        timeout: 2000,
        dialog: false,
      },
      // datepicker
      activePicker: null,
      menu: false,
      dialog: false,

      //validate
      rules: [
        value => !! value || "Campo obrigatório"
      ],
      cpfRules: [
        value => !!value || "CPF é obrigatório",
        value => this.validarCpf(value) || "CPF inválido"
      ],
      valid: false,
      name: '',
      cpf: '',
      date: null,
    };
  },
  methods: {
    // Requisições do carregamento
    searchProfessioanls() {
      if (this.selects.specialtie === null) {
        this.widgets.text = 'Selecione a especialidade.'
        this.widgets.snackbar = true

        return 0;
      }

      let id = this.selects.specialtie
      this.widgets.overlay = true;
      this.$http
        .post(`/professional/list?id=${id}`)
        .then((res) => {
          this.professionals = res.data;
        })
        .then(() => {
          this.widgets.text = 'Sucesso!'
          this.widgets.overlay = false
          this.widgets.snackbar = true
        })
        .catch((err) => {
          console.log(err)
          this.widgets.overlay = true;
        });
    },
    getTableDataRow(data) {
      this.widgets.dialog = true
      this.tempProfId = data.profissional_id;
    },
    checkForm() {
      if (this.$refs.formModal.validate()) {
        this.save()
      }
    },
    // Envia form de consulta
    save() {
      this.$http
        .post('/patient/appointment', this.formData)
        .then((res) => {
          console.log(res.data)
        })
        .then(() => {
          setTimeout(() => {
            this.widgets.text = 'Solicitação enviada!'
            this.widgets.snackbar = true
          }, 1000)
        })
        .catch((err) => {
          console.log(err)
          this.widgets.text = 'Ooops... algo errado aconteceu!'
          this.widgets.snackbar = true
      });

      this.widgets.dialog = false
      this.reset

    },
    saveNewDate(date) {
        this.$refs.menu.save(date)
    },
    reset() {
        this.name = ''
        this.cpf = ''
        this.selects.how = null
    },
    validarCpf(cpf) {
        var soma = 0;
        var resto;
        var strCPF = cpf.replace(/[^0-9]+/g, '');

        if (strCPF === '00000000000') {
          return false;
        }

        for (var i = 1; i <= 9; i++) {
          soma += parseInt(strCPF.substring(i - 1, i)) * (11 - i);
        }

        resto = soma * 10 % 11;

        if (resto === 10 || resto === 11) {
          resto = 0;
        }

        if (resto !== parseInt(strCPF.substring(9, 10))) {
          return false;
        }

        soma = 0;

        for (var _i = 1; _i <= 10; _i++) {
          soma = soma + parseInt(strCPF.substring(_i - 1, _i)) * (12 - _i);
        }

        resto = soma * 10 % 11;

        if (resto === 10 || resto === 11) {
          resto = 0;
        }

        if (resto !== parseInt(strCPF.substring(10, 11))) {
          return false;
        }

        return true;
    },
  },
  computed: {
    formData() {
      let form = {
        specialty_id: this.selects.specialtie,
        professional_id: this.tempProfId,
        name: this.name,
        cpf: this.cpf,
        birthdate: this.date,
        source_id: this.selects.how
      }
      return form
    }
  },
};
</script>
