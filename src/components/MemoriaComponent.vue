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
            <b-list-group-item v-for="bloco in blocosTotais" :key="bloco">
              {{ bloco }}
            </b-list-group-item>
          </b-list-group>
        </b-col>
        <b-col>
          <b-list-group>
            <b-list-group-item style="border-left: 1px solid red; border-right: 1px solid red;" v-for="(index, linha) in linhasTotais" :key="linha" :class="{ bordered: cache.n > 1 ? linha % cache.n == 0 : 1 }">
              {{ linha }}              
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
        quantidade: 16
      },
      cache: {
        n: 4,
        linhas: 16,
        // tamanho: 0,
        tag: 0
      },
      blocosTotais: [],
      linhasTotais: [],
      sequencia: '1,2,5,6,9',
      start: true
    }
  },
  methods: {
    montarSimulacao: function () {
      for (let i = 0; i < this.memoria.quantidade; i++) {
        this.blocosTotais.push(123)
      }
      for (let i = 0; i < this.cache.linhas; i++) {
        this.linhasTotais.push(0)
      }
    },
    iniciarSimulacao: function () {
      let itens = this.sequencia.split(',')
      // alert(itens)
      for (let i = 0; i < itens.length; i++) {
        // alert(itens[i])
        
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
</script>

<style>
.container {
  padding-top: 1rem !important;
}
.bordered {
  border-top: 1px solid red !important;
}

</style>
