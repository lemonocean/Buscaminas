<template>
  <div class="tablero">
    <div class="niveles">
      <span>Nivel:</span>
      <span @click="seleccionarNivel(1)" class="nivel" :class="{ 'nivel-seleccionado': nivelActual.nivel == 1 }">1</span>
      <span @click="seleccionarNivel(2)" class="nivel" :class="{ 'nivel-seleccionado': nivelActual.nivel == 2 }">2</span>
      <span @click="seleccionarNivel(3)" class="nivel" :class="{ 'nivel-seleccionado': nivelActual.nivel == 3 }">3</span>
    </div>
    <div class="panel">
      <div class="marcador minas-restantes">
        999
      </div>
      <div class="cara">
        <span>🙂</span>
      </div>
      <div class="marcador segundos">
        999
      </div>
    </div>
    <div class="matriz">
      <cuadro :info="item" v-for="(item, index) in cuadros" :key="index" :style="'grid-row: ' + item.fila + '; grid-column:' + item.columna + ';'" />
    </div>
  </div>
</template>

<script>
import Cuadro from './Cuadro.vue'

export default {
  components: { Cuadro },
  data () {
    return {
      cuadros: [],
      nivelPrincipiante: {
        nivel: 1,
        filas: 9,
        columnas: 9,
        minas: 10
      },
      nivelIntermedio: {
        nivel: 2,
        filas: 16,
        columnas: 16,
        minas: 40
      },
      nivelExperto: {
        nivel: 3,
        filas: 16,
        columnas: 30,
        minas: 99
      },
      nivelActual: null,
      minas: []
    }
  },
  created () {
    this.nivelActual = this.nivelPrincipiante
    this.iniciarNivel()
  },
  methods: {
    seleccionarNivel (nivel) {
      if (this.nivelActual.nivel == nivel) { return }

      if (nivel == 1) { this.nivelActual = this.nivelPrincipiante }
      else if (nivel == 2) { this.nivelActual = this.nivelIntermedio }
      else { this.nivelActual = this.nivelExperto }

      this.iniciarNivel()
    },
    iniciarNivel () {
      let filas = this.nivelActual.filas
      let columnas = this.nivelActual.columnas
      let totalCuadros = filas * columnas

      this.cuadros = []
      let indices = []

      for (let i = 0; i < totalCuadros; i++) {
        let cuadro = {
          valor: '',
          fila: Math.floor(i / columnas) + 1,
          columna: (i % columnas) + 1 
        }

        this.cuadros.push(cuadro)
        indices.push(i)
      }

      for (let i = 0; i < this.nivelActual.minas; i++) {
        let posicion = Math.floor(Math.random() * (indices.length - 1))
        let indice = indices[posicion]

        this.cuadros[indice].valor = '💣'
        this.minas.push(this.cuadros[indice])

        indices.splice(posicion, 1)
      }
    }
  }
}
</script>


<style>

@import url('https://fonts.googleapis.com/css?family=Roboto+Mono');

html {
  font-family: 'Roboto Mono', monospace;
}

.tablero {
  display: grid;
  justify-content: center;
  background-color: #bdbdbd;
  padding: 10px;
}

.niveles {
  display: grid;
  grid-auto-flow: column;
  grid-gap: 10px;
  font-size: 24px;
  padding: 5px;
  justify-content: start;
  align-items: center;
}

.nivel {
  width: 32px;
  height: 32px;
  color: #5c5c5c;
  text-align: center;
  vertical-align: center;
  cursor: pointer;
}

.nivel-seleccionado {
  color: #fff !important;
  background-color: #5f9cff;
  border-style: solid;
  border-width: 2px;
  border-radius: 50%;
  cursor: default;
}

.panel {
  display: grid;
  grid-auto-flow: column;
  font-size: 30px;
}

.marcador {
  background-color: black;
  color: red;
  height: 40px;
}

.minas-restantes {
  justify-self: start;
}

.cara {
  display: grid;
  justify-content: center;
  align-items: center;
  justify-self: center;
  width: 40px;
  height: 40px;
}

.segundos {
  justify-self: end;
}

.matriz {
  display: grid;
  background-color: #7b7b7b;
}

</style>