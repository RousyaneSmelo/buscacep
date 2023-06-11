<template>
  <v-app>
    <v-main>
      <v-container class="pt-12">
        <v-card class="mx-auto" max-width="500">
          <v-card-title class="text-center">
            <h2 class="headline">Consulta de CEP</h2>
          </v-card-title>
          <v-card-text>
            <v-form>
              <v-row>
                <v-col cols="12" sm="6">
                  <v-text-field
                    v-model="cep"
                    label="CEP"
                    outlined
                    v-mask="'#####-###'"
                    :placeholder="'_____-___'"
                    :class="{ 'error-field': error }"
                  ></v-text-field>
                  <p class="error-message" v-if="notFound">CEP incorreto. Verifique o CEP digitado.</p>
                </v-col>
                <v-col cols="12" sm="6">
                  <v-btn color="primary" @click="fetchCep">Buscar</v-btn>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12">
                  <v-text-field
                    v-model="logradouro"
                    label="Logradouro"
                    outlined
                    disabled
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" sm="6">
                  <v-text-field
                    v-model="bairro"
                    label="Bairro"
                    outlined
                    disabled
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="6">
                  <v-text-field
                    v-model="localidade"
                    label="Localidade"
                    outlined
                    disabled
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" sm="6">
                  <v-text-field
                    v-model="uf"
                    label="UF"
                    outlined
                    disabled
                  ></v-text-field>
                </v-col>
              </v-row>
              <!-- <v-layout v-if="showCopy == true"> -->
                <v-row>
                  <v-col cols="12" v-if="cep != ''">
                    <h3 class="text-center" v-if="!error && !notFound">Copie todos os dados do CEP: <br> {{ formattedAddress }}</h3>
                    <p class="text-center" v-if="error">CEP não localizado.</p>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" v-if="cep != ''">
                    <v-btn color="primary" @click="copyAddress" v-if="!error && !notFound">Copiar</v-btn>
                  </v-col>
                </v-row>
              <!-- </v-layout> -->
            </v-form>
          </v-card-text>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      cep: '',
      logradouro: '',
      bairro: '',
      localidade: '',
      uf: '',
      error: false,
      notFound: false,
      // showCopy: false
    };
  },
  computed: {
    formattedAddress() {
      return `${this.logradouro}, ${this.bairro}, ${this.localidade} - ${this.uf}`;
    }
  },
  methods: {
    fetchCep() {
      const cep = this.cep.replace(/\D/g, '');
      const url = `https://viacep.com.br/ws/${cep}/json/`;

      axios.get(url)
        .then(response => {
          const data = response.data;
          if (data.erro) {
            this.error = true;
            this.notFound = false;
            this.logradouro = '';
            this.bairro = '';
            this.localidade = '';
            this.uf = '';
          } else {
            this.error = false;
            this.notFound = false;
            this.logradouro = data.logradouro;
            this.bairro = data.bairro;
            this.localidade = data.localidade;
            this.uf = data.uf;
          }
        })
        .catch(error => {
          console.error(error);
          this.error = false;
          this.notFound = true;
          this.logradouro = '';
          this.bairro = '';
          this.localidade = '';
          this.uf = '';
        });
    },
    copyAddress() {
      this.showCopy = true;
      const textArea = document.createElement('textarea');
      textArea.value = this.formattedAddress;
      document.body.appendChild(textArea);
      textArea.select();
      document.execCommand('copy');
      document.body.removeChild(textArea);
    }
  }
};
</script>

<style scoped>
.card {
  background-color: #f5f5f5;
}

.v-text-field {
  width: 100%;
}

.headline {
  font-size: 24px;
  margin-bottom: 20px;
}

.text-center {
  text-align: center;
}

.pt-12 {
  padding-top: 12px;
}

.error-field .v-input__control {
  border-color: red !important;
}

.error-message {
  color: red;
  margin-top: 5px;
}
</style>
