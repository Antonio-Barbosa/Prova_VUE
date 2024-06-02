<template>
  <div class="about">
    <h1>Vocábulos</h1>

    <p><label for="termo">Termo:</label></p>
    <input id="termo" type="text" v-model="vocabulo.termo" />
    <p><label for="significado">Significado:</label></p>
    <input id="significado" type="text" v-model="vocabulo.significado" />
    <p><label for="versao">Versão:</label></p>
    <input id="versão" type="number" v-model="vocabulo.versao" />

    <button @click="cadastrarVocabulo">Cadastrar novo vocabulo</button>

    <p>{{ erro }}</p>

    <h2>Buscar vocábulo</h2>
    <p><label for="buscarTermo">Termo:</label></p>
    <input id="buscarTermo" type="text" v-model="buscarTermo" />
    <p><label for="buscarVersao">Versão:</label></p>
    <input id="buscarVersao" type="number" v-model="buscarVersao" />

    <button @click="buscarVocabulo">Buscar vocábulo</button>

    <p>{{ erroBusca }}</p>

    <table>
      <thead>
        <td>Id</td>
        <td>Termo</td>
        <td>Significado</td>
        <td>Versão</td>
        <td>Situação</td>
      </thead>

      <tbody>
        <tr v-for="vocabulo in vocabulos" :key="vocabulo.id">
          <td>{{ vocabulo.id }}</td>
          <td>{{ vocabulo.termo }}</td>
          <td>{{ vocabulo.significado }}</td>
          <td>{{ vocabulo.versao }}</td>
          <td>{{ vocabulo.dataHoraDesativacao ? 'desativado' : 'ativado' }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import axios from 'axios'
import { onMounted, ref } from 'vue'

const api_url = 'https://8080-mineda-springboot3app-8p7xyi1qypl.ws-us114.gitpod.io/vocabulo'
const vocabulo = ref({
  id: null,
  termo: '',
  significado: '',
  versao: null,
  situacao: false
})
const vocabulos = ref()
const erro = ref('')
const buscarTermo = ref('')
const buscarVersao = ref(null)
const erroBusca = ref('')

async function cadastrarVocabulo() {
  erro.value = ''
  try {
    await axios.post(api_url, vocabulo.value)
    // Limpar os campos após o cadastro
    vocabulo.value = {
      id: null,
      termo: '',
      significado: '',
      versao: null,
      situacao: false
    }
    // Recarregar a lista de vocábulos
    getAll()
  } catch (e) {
    erro.value = (e as Error).message
  }
}

async function getAll() {
  try {
    vocabulos.value = (await axios.get(api_url)).data
  } catch (e) {
    erro.value = (e as Error).message
    console.log(e)
  }
}

async function buscarVocabulo() {
  erroBusca.value = ''
  try {
    vocabulos.value = (
      await axios.get(`${api_url}/${buscarTermo.value}/${buscarVersao.value}`)
    ).data
  } catch (e) {
    erroBusca.value = (e as Error).message
    console.log(e)
  }
}

onMounted(() => {
  getAll()
})
</script>

<style scoped>
.about {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
  color: red;
}

h2 {
  text-align: center;
  color: red;
}

label {
  display: block;
  margin: 10px 0 5px;
  font-weight: bold;
}

input[type='text'],
input[type='number'] {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

button {
  display: block;
  width: 100%;
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

p {
  color: red;
  text-align: center;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

thead {
  background-color: #f8f9fa;
  color: black;
}

th,
td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #ddd;
  background-color: white;
  color: black;
  font-size: larger;
}

tr:hover {
  background-color: #f1f1f1;
}
</style>
