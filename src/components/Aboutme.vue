<template>
  <v-container>
    <v-row>
      <v-col col="12">
        <v-card class="mx-auto" max-width="344">
          <v-img src="https://media-exp1.licdn.com/dms/image/C4E03AQEqrZpTVu_46Q/profile-displayphoto-shrink_400_400/0/1552147289008?e=1653523200&v=beta&t=M-pMC56q6WMaXVAUmt50jztHMT8Dt5GW3uQHX3PGVXU" height="200px"></v-img>
          <v-card-title>João Ferreira</v-card-title>
          <v-card-subtitle>Desenvolvedor</v-card-subtitle>
          <v-card-actions>
            <v-btn>: )</v-btn>
            <v-spacer></v-spacer>
            <v-btn icon @click="show = !show">
              <v-icon>{{ show ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
            </v-btn>
          </v-card-actions>
          <v-expand-transition>
            <div v-show="show">
              <v-divider></v-divider>
              <v-card-text>Stay safe...</v-card-text>
            </div>
          </v-expand-transition>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "AboutMe",
  data: () => ({
    show: false,
  }),
};
</script>