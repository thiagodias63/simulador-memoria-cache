<template>
  <div class="container">
    <b-container>
      <h2> Simulador de Memoria </h2>
      <br>
      <h4>Memória Principal</h4>
      <b-row>
        <b-col>
          <b-form-group label="Tamanho da MP:" :description="memoria.tamanho / (1024 * 1024) + 'GB'">
            <b-form-input v-model="memoria.tamanho" type="number"></b-form-input>
          </b-form-group>
        </b-col>
        <b-col>
          <b-form-group label="Tamanho do Bloco:" :description="memoria.bloco + 'B'">
            <b-form-input v-model="memoria.bloco" type="number"></b-form-input>
          </b-form-group>
        </b-col>
      </b-row>
      <h5>Quantidade de Blocos: {{ tamanhoBlocoMP }}</h5>
      <br><hr><br>
      <h4>Memória Cache</h4>
      <b-row>
        <b-col>
          <b-form-group label="Quantidade de Linhas:">
            <b-form-input v-model="cache.linha" type="number"></b-form-input>
          </b-form-group>
        </b-col>
        <b-col v-if="cache.linha && memoria.quantidade">
          <b-form-group label="N de Linhas:" :description="cache.quantidade / cache.n">
            <b-form-input v-model="cache.n" type="number"></b-form-input>
          </b-form-group>
        </b-col>
        <b-col>
          <b-form-group label="Tamanho da TAG:">
            <b-form-input v-model="cache.tag" disabled type="number"></b-form-input>
          </b-form-group>
        </b-col>
        <b-col>
          <b-form-group label="Tamanho da Linha:">
            <b-form-input v-model="memoria.bloco" disabled type="number"></b-form-input>
          </b-form-group>
        </b-col>
      </b-row>
      <h5>Tamanho da Cache: {{ cache.linha * (cache.tag * memoria.bloco) }}</h5>
      <br><hr><br>
    </b-container>
  </div>
</template>

<script>
export default {
  name: 'MemoriaComponent',
  data () {
    return {
      memoria: {
        tamanho: 0,
        bloco: 0,
        quantidade: 0
      },
      cache: {
        n: 0,
        linha: 0,
        tamanho: 0,
        tag: 0
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

</style>
