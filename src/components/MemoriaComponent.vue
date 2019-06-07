<template>
  <div class="container">
    <b-container v-if="!start">
      <h2> Simulador de Memoria </h2>
      <br>
      <h4>Memória Principal</h4>
      <b-row>
        <b-col>
          <b-form-group label="Tamanho da MP:" :description="memoria.tamanho / (1024 * 1024) + 'GB'">
            <b-form-input v-model.number="memoria.tamanho" type="number"></b-form-input>
          </b-form-group>
        </b-col>
        <b-col>
          <b-form-group label="Tamanho do Bloco:" :description="memoria.bloco + 'B'">
            <b-form-input v-model.number="memoria.bloco" type="number"></b-form-input>
          </b-form-group>
        </b-col>
      </b-row>
      <h5>Quantidade de Blocos: {{ tamanhoBlocoMP }}</h5>
      <br><hr><br>
      <h4>Memória Cache</h4>
      <b-row>
        <b-col>
          <b-form-group label="Quantidade de Linhas:">
            <b-form-input v-model.number="cache.linhas" type="number"></b-form-input>
          </b-form-group>
        </b-col>
        <b-col v-if="cache.linhas && memoria.quantidade">
          <b-form-group label="N de Linhas:" :description="cache.quantidade / cache.n">
            <b-form-input v-model.number="cache.n" type="number"></b-form-input>
          </b-form-group>
        </b-col>
        <b-col>
          <b-form-group label="Tamanho da TAG:">
            <b-form-input v-model.number="cache.tag" disabled type="number"></b-form-input>
          </b-form-group>
        </b-col>
        <b-col>
          <b-form-group label="Tamanho da Linha:">
            <b-form-input v-model.number="memoria.bloco" disabled type="number"></b-form-input>
          </b-form-group>
        </b-col>
      </b-row>
      <h5>Tamanho da Cache: {{ cache.linhas * memoria.bloco }}B </h5>
      <br><hr><br>
      <b-row>
        <b-col>
          <b-form-group label="Sequência de Execução:">
            <b-form-input v-model="sequencia" type="text"></b-form-input>
          </b-form-group>
        </b-col>
      </b-row>
      <b-row>
        <b-col>
          <b-button @click="start = true" button variant="info"> Enviar </b-button>
        </b-col>
      </b-row>
    </b-container>
    <b-container v-else>
      <b-row>
        <h3 style="margin-left: 45%"> Simulador </h3>
      </b-row>
      <b-row style="padding-bottom: 1rem;">
        <b-button style="margin-left: 1rem;" button variant="info" @click="montarSimulacao()"> Montar </b-button>
        <b-button style="margin-left: 1rem;" button variant="info" @click="iniciarSimulacao()"> Iniciar </b-button>
        <b-button style="margin-left: 59rem" variant="outline-primary" @click="start = false"> Voltar </b-button>
      </b-row>      
      <!-- <b-row>
        <b-col>
          <b-list-group>
            <b-list-group-item v-for="bloco in memoria.quantidade" :key="bloco">
              {{ bloco }}
            </b-list-group-item>
          </b-list-group>
        </b-col>
        <b-col>
          <b-list-group>
            <b-list-group-item style="border-left: 1px solid red; border-right: 1px solid red;" v-for="linha in cache.linhas" :key="linha" :class="{ bordered: cache.n > 1 ? linha % cache.n == 1 : 1 }">
              <! {{ linha % cache.n == 1 }}  0
            </b-list-group-item>
          </b-list-group>
        </b-col>
      </b-row> --> 
      
      <b-row>
        <b-col>
          <b-list-group>
            <b-list-group-item v-for="(value, key, bloco) in blocosTotais" :key="bloco" :class="{ redered: value.cor == '1'}">
              {{ value.valor }}
            </b-list-group-item>
          </b-list-group>
        </b-col>
        <b-col>
          <b-list-group>
            <b-list-group-item style="border-left: 1px solid red; border-right: 1px solid red;" 
              v-for="(value, key) of linhasTotais" :key="key" 
              :class="{ bordered: cache.n > 1 ? key % cache.n == 0 : 1, redered: value.cor == '1', oranged: value.cor == '2' }"
              >
              Valor: {{ value.valor }} 
              <span v-if="metodo == 'LRU'"> Ultima Referência: {{value.ultimaReferencia}} </span>
              <span v-if="metodo == 'FIFO'"> Entrada: {{value.entrada}} </span>
              <span v-if="metodo == 'LFU'"> Acessos: {{value.acessos}} </span>
            </b-list-group-item>
          </b-list-group>
        </b-col>
      </b-row>
      <br><br><hr><br><br>

    </b-container>
  </div>
</template>

<script>
export default {
  name: 'MemoriaComponent',
  data () {
    return {
      memoria: {
        tamanho: 1024,
        bloco: 64,
        quantidade: 64
      },
      cache: {
        n: 4,
        linhas: 16,
        // tamanho: 0,
        tag: 0
      },
      blocosTotais: [],
      linhasTotais: [],
      sequencia: '1,1,1,1,5,14,10,15',
      metodos: ['LRU', 'FIFO', 'LFU'],
      metodo: 'LFU',
      start: true
    }
  },
  methods: {
    getRandomInt: function (min, max) {
      min = Math.ceil(min);
      max = Math.floor(max);
      return Math.floor(Math.random() * (max - min)) + min;
    },
    montarSimulacao: function () {
      for (let i = 0; i < this.memoria.quantidade; i++) {
        this.blocosTotais.push({valor: i, cor: '0'})
      }
      for (let i = 0; i < this.cache.linhas; i++) {
        this.linhasTotais.push({valor: 0, cor: '0', acessos: this.getRandomInt(1, 15), entrada: this.getRandomInt(1, 10), ultimaReferencia: this.getRandomInt(1, 1024)})
      }
    },
    iniciarSimulacao: function () {
      let itens = this.sequencia.split(',')
      let numeroConjuntos = this.cache.linhas / this.cache.n
      for (let i = 0; i < itens.length; i++) {
        this.blocosTotais[itens[i]].cor = 1
      }

      let conjunto = -1
      for (let i = 0; i < itens.length; i++) {
        conjunto = itens[i] % numeroConjuntos
        let inicioConjunto = this.cache.n * conjunto
        let fimConjunto = inicioConjunto + this.cache.n - 1
        let comparar = true

        for (let j = inicioConjunto; j < fimConjunto + 1; j++) {
          if (j == fimConjunto && this.linhasTotais[j].valor > 0) {
            if (this.metodo == 'LRU') {
              let menorReferencia = 1025
              let indiceEscolhido = -1
              for (let k = inicioConjunto; k < fimConjunto + 1; k++) {
                if (menorReferencia > this.linhasTotais[k].ultimaReferencia) {
                  menorReferencia = this.linhasTotais[k].ultimaReferencia
                  indiceEscolhido = k
                }
              }
              alert('Troca pela ultima referência! Ultima referência: ' + menorReferencia)
              this.linhasTotais[indiceEscolhido].valor = this.blocosTotais[itens[i]].valor
              this.linhasTotais[indiceEscolhido].cor = 1
            } else if (this.metodo == 'FIFO') {
              let primeiro = 11
              let indiceEscolhido = -1
              for (let k = inicioConjunto; k < fimConjunto + 1; k++) {
                if (primeiro > this.linhasTotais[k].entrada) {
                  primeiro = this.linhasTotais[k].entrada
                  indiceEscolhido = k
                }
              }
              alert('Troca pelo primeiro a entrar! Quando entrou:' + primeiro)
              this.linhasTotais[indiceEscolhido].valor = this.blocosTotais[itens[i]].valor
              this.linhasTotais[indiceEscolhido].cor = 1
            } else { // LFU
              let acessos = 16
              let indiceEscolhido = -1
              for (let k = inicioConjunto; k < fimConjunto + 1; k++) {
                if (acessos > this.linhasTotais[k].acessos) {
                  acessos = this.linhasTotais[k].acessos
                  indiceEscolhido = k
                }
              }
              alert('Troca pelo menos acessado! Nº de acessos ' + acessos)
              this.linhasTotais[indiceEscolhido].valor = this.blocosTotais[itens[i]].valor
              this.linhasTotais[indiceEscolhido].cor = 1
            }
            comparar = false  
          } else if (comparar == true && this.linhasTotais[j].valor == 0) {
            this.linhasTotais[j].valor = this.blocosTotais[itens[i]].valor
            this.linhasTotais[j].cor = 2
            comparar = false
          }
        }
      }
    }
  },
  computed: {
    tamanhoBlocoMP: function () {
      // 1048576 - 1GB
      if (this.memoria.tamanho > 0 && this.memoria.bloco > 0) {
        this.memoria.quantidade = this.memoria.tamanho / this.memoria.bloco
        return this.memoria.quantidade
      } else {
        this.memoria.quantidade = 0
        this.cache.tag = 0
        return 0
      }
    },
    tamanhoTag: function () {
      if (this.memoria.quantidade > 0) {
        tamanhoMP = Math.log2(tamanhoMP)
        tamanhoBloco = Math.log2(tamanhoBloco)
        this.cache.tag = tamanhoMP - tamanhoBloco
        return this.memoria.quantidade
      } else {
        this.memoria.quantidade = 0
        this.cache.tag = 0
        return 0
      }
    }
  }
}

/*
this.linhasTotais[inicioConjunto].ultimaReferencia = this.blocosTotais[itens[i]].valor
this.linhasTotais[inicioConjunto].cor = 1
*/
</script>

<style>
.container {
  padding-top: 1rem !important;
}
.bordered {
  border-top: 1px solid red !important;
}
.redered {
  background-color: grey !important;
}
.oranged {
  background-color: orangered;
}
.blued {
  background-color: blue;
}
</style>
