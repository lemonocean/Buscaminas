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
        {{ minasRestantesCifras }}
      </div>
      <div class="cara" @click="iniciarNivel">
        <span>{{ cara }}</span>
      </div>
      <div class="marcador segundos">
        {{ segundosCifras }}
      </div>
    </div>
    <div class="matriz">
      <cuadro 
        @mousedown.left.native="mouseDownLeft(cuadro)"
        @mousedown.right.native="mouseDownRight(cuadro)"
        @mouseup.left.native="mouseUpLeft(cuadro)"
        @mouseup.right.native="mouseUpRight(cuadro)"
        @mouseenter.native="mouseEnter(cuadro)"
        @mouseleave.native="mouseLeave(cuadro)"
        @contextmenu.prevent.native
      :info="cuadro" v-for="(cuadro, index) in cuadros" :key="index" :style="'grid-row: ' + cuadro.fila + '; grid-column:' + cuadro.columna + ';'" />
    </div>
  </div>
</template>

<script>
import Cuadro from './Cuadro.vue'

export default {
  components: { Cuadro },
  data () {
    return {
      botonIzquierdo: false,
      botonDerecho: false,
      cara: '🙂',
      cuadros: [],
      colores: ['', 'uno', 'dos', 'tres', 'cuatro', 'cinco', 'seis', 'siete', 'ocho'],
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
      minas: [],
      minasRestantes: 0,
      segundos: 0,
      inicio: false,
      timer: null,
      jugando: false,
      cuadrosRestantes: 0
    }
  },
  computed: {
    minasRestantesCifras () {
      let cifras = this.minasRestantes.toString()

      if (cifras.length == 1) {
        cifras = '00' + cifras
      }
      else if (cifras.length == 2) {
        cifras = '0' + cifras
      }

      return cifras
    },
    segundosCifras () {
      let cifras = this.segundos.toString()

      if (cifras.length == 1) {
        cifras = '00' + cifras
      }
      else if (cifras.length == 2) {
        cifras = '0' + cifras
      }

      return cifras
    }
  },
  created () {
    this.nivelActual = this.nivelPrincipiante
    this.iniciarNivel()
  },
  methods: {
    agregarSuspenso (cuadro) {
      if (this.jugando) {
        cuadro.suspenso = true

        if (this.botonDerecho) {
          cuadro.vecinos.forEach(v => {
            if (!this.cuadros[v].bandera) {
              this.cuadros[v].suspenso = true
            }
          })
        }

        this.cara = '😮'
      }
    },
    quitarSuspenso (cuadro) {
      if (this.jugando) {
        cuadro.suspenso = false

        cuadro.vecinos.forEach(v => {
          this.cuadros[v].suspenso = false
        })

        this.cara = '🙂'
      }
    },
    mouseDownLeft(cuadro) {
      if (this.jugando) {
        this.botonIzquierdo = true

        this.agregarSuspenso(cuadro)
      }
    },
    mouseDownRight(cuadro) {
      if (this.jugando) {
        this.botonDerecho = true

        if (!this.botonIzquierdo) {
          this.cambiarMinasRestantes(cuadro)
        }
        else {
          this.agregarSuspenso(cuadro)
        }
      }
    },
    mouseUpLeft(cuadro) {
      if (this.jugando) {
        this.botonIzquierdo = false

        if (cuadro.inicial) {
          this.activarCuadro(cuadro)
        }
        else if (this.botonDerecho) {
          this.despejarCuadro(cuadro)
        }

        this.quitarSuspenso(cuadro)
      }
    },
    mouseUpRight(cuadro) {
      if (this.jugando) {
        this.botonDerecho = false

        if (this.botonIzquierdo) {
          this.despejarCuadro(cuadro)
        }
      }
    },
    mouseEnter(cuadro) {
      if (this.jugando && this.botonIzquierdo) {
        this.agregarSuspenso(cuadro)
      }
    },
    mouseLeave(cuadro) {
      if (this.jugando && cuadro.suspenso) {
        this.quitarSuspenso(cuadro)
      }
    },
    detenerTiempo () {
      if (this.timer) {
        clearInterval(this.timer)
      }
    },
    seleccionarNivel (nivel) {
      if (this.nivelActual.nivel == nivel) { return }

      if (nivel == 1) { this.nivelActual = this.nivelPrincipiante }
      else if (nivel == 2) { this.nivelActual = this.nivelIntermedio }
      else { this.nivelActual = this.nivelExperto }

      this.iniciarNivel()
    },
    iniciarNivel () {
      this.botonIzquierdo = false
      this.botonDerecho = false
      this.cara = '🙂'
      this.detenerTiempo()
      this.minasRestantes = this.nivelActual.minas
      this.segundos = 0
      this.inicio = false

      let filas = this.nivelActual.filas
      let columnas = this.nivelActual.columnas
      let totalCuadros = filas * columnas
      
      this.cuadrosRestantes = totalCuadros - this.nivelActual.minas

      this.cuadros = []
      let indices = []

      for (let i = 0; i < totalCuadros; i++) {
        let cuadro = {
          suspenso: false,
          inicial: true,
          bandera: false,
          valor: '',
          fila: Math.floor(i / columnas) + 1,
          columna: (i % columnas) + 1,
          vecinos: [],
          claseValor: ''
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

      for (let i = 0; i < totalCuadros; i++) {
        let cuadro = this.cuadros[i]

        if (cuadro.columna == 1) {
          if (cuadro.fila == 1) {
            cuadro.vecinos.push(i + 1)
            cuadro.vecinos.push(i + columnas)
            cuadro.vecinos.push(i + columnas + 1)
          }
          else if (cuadro.fila == filas) {
            cuadro.vecinos.push(i + 1)
            cuadro.vecinos.push(i - columnas)
            cuadro.vecinos.push(i - columnas + 1)
          }
          else {
            cuadro.vecinos.push(i + 1)
            cuadro.vecinos.push(i + columnas)
            cuadro.vecinos.push(i + columnas + 1)
            cuadro.vecinos.push(i - columnas)
            cuadro.vecinos.push(i - columnas + 1)
          }
        }
        else if (cuadro.columna == columnas) {
          if (cuadro.fila == 1) {
            cuadro.vecinos.push(i - 1)
            cuadro.vecinos.push(i + columnas)
            cuadro.vecinos.push(i + columnas - 1)
          }
          else if (cuadro.fila == filas) {
            cuadro.vecinos.push(i - 1)
            cuadro.vecinos.push(i - columnas)
            cuadro.vecinos.push(i - columnas - 1)
          }
          else {
            cuadro.vecinos.push(i - 1)
            cuadro.vecinos.push(i + columnas)
            cuadro.vecinos.push(i + columnas - 1)
            cuadro.vecinos.push(i - columnas)
            cuadro.vecinos.push(i - columnas - 1)
          }
        }
        else {
          if (cuadro.fila == 1) {
            cuadro.vecinos.push(i - 1)
            cuadro.vecinos.push(i + 1)
            cuadro.vecinos.push(i + columnas - 1)
            cuadro.vecinos.push(i + columnas)
            cuadro.vecinos.push(i + columnas + 1)
          }
          else if (cuadro.fila == filas) {
            cuadro.vecinos.push(i - 1)
            cuadro.vecinos.push(i + 1)
            cuadro.vecinos.push(i - columnas - 1)
            cuadro.vecinos.push(i - columnas)
            cuadro.vecinos.push(i - columnas + 1)
          }
          else {
            cuadro.vecinos.push(i - 1)
            cuadro.vecinos.push(i + 1)
            cuadro.vecinos.push(i + columnas - 1)
            cuadro.vecinos.push(i + columnas)
            cuadro.vecinos.push(i + columnas + 1)
            cuadro.vecinos.push(i - columnas - 1)
            cuadro.vecinos.push(i - columnas)
            cuadro.vecinos.push(i - columnas + 1)
          }
        }
        
        if (cuadro.valor != '💣') {
          let minas = cuadro.vecinos.filter(v => this.cuadros[v].valor == '💣').length

          if (minas > 0) {
            cuadro.valor = minas
            cuadro.claseValor = 'numero ' + this.colores[minas]
          }
        }
      }

      this.jugando = true
    },
    activarCuadro (cuadro) {
      if (this.jugando && cuadro.inicial && !cuadro.bandera) {
        cuadro.inicial = false

        if (!this.inicio) {
          this.timer = setInterval(() => {
            this.segundos++
          }, 1000)

          this.inicio = true
        }

        if (cuadro.valor == '💣') {
          this.explosion(cuadro)
        }
        else {
          this.cuadrosRestantes--

          if (this.cuadrosRestantes <= 0) {
            this.ganar()
          }
          else if (cuadro.valor == '') {
            cuadro.vecinos.forEach(v => {
              this.activarCuadro(this.cuadros[v])
            })
          }
        }        
      }
    },
    despejarCuadro (cuadro) {
      if (!cuadro.inicial && cuadro.valor != '') {
        let banderas = cuadro.vecinos.filter(v => this.cuadros[v].bandera).length

        if (cuadro.valor == banderas) {
          cuadro.vecinos.forEach(v => {
            this.activarCuadro(this.cuadros[v])
          })
        }
      }
    },
    cambiarMinasRestantes (cuadro) {
      if (this.jugando && cuadro.inicial) {
        cuadro.bandera = !cuadro.bandera
        this.minasRestantes += cuadro.bandera ? -1 : 1
      }
    },
    explosion (cuadro) {
      this.jugando = false
      this.detenerTiempo()

      this.minas.forEach(mina => {
        if (!mina.bandera) {
          mina.inicial = false
        }
      })

      let banderas = this.cuadros.filter(c => c.bandera)

      banderas.forEach(c => {
        if (c.valor != '💣') {
          c.bandera = false
          c.valor = '❌'
          c.inicial = false
        }
      })

      cuadro.valor = '💥'
      this.cara = '🙁'
    },
    ganar () {
      this.jugando = false
      this.detenerTiempo()

      this.minas.forEach(mina => {
        mina.bandera = true
      })

      this.minasRestantes = 0
      this.cara = '😎'
    }
  }
}
</script>


<style>

@import url('https://fonts.googleapis.com/css?family=Roboto+Mono');

html {
  font-family: 'Roboto Mono', monospace;
}

.numero {
  font-size: 20px;
  font-weight: bold;
}

.uno {
  color: blue;  
}

.dos {
  color: green;
}

.tres {
  color: red;
}

.cuatro {
  color: darkblue;
}

.cinco {
  color: brown;
}

.seis {
  color: darkcyan;
}

.siete {
  color: #9e3c9e;
}

.ocho {
  color: #ffc400;
}

.tablero {
  display: grid;
  justify-content: center;
  background-color: #bdbdbd;
  padding: 10px;
  user-select: none;
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
  margin-top: 10px;
  padding: 10px;
  border-top-color: #818181;
  border-left-color: #818181;
  border-bottom-color: #fff;
  border-right-color: #fff;
  border-style: solid;
  border-width: 2px;
}

.marcador {
  background-color: black;
  color: red;
  height: 40px;
  padding: 2px;
  border-top-color: #818181;
  border-left-color: #818181;
  border-bottom-color: #fff;
  border-right-color: #fff;
  border-style: solid;
  border-width: 1px;
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
  font-size: 24px;
  border-top-color: #fff;
  border-left-color: #fff;
  border-bottom-color: #818181;
  border-right-color: #818181;
  border-style: solid;
  border-width: 2px;
  cursor: pointer;
}

.segundos {
  justify-self: end;
}

.matriz {
  display: grid;
  background-color: #7b7b7b;
  padding: 2px;
  margin-top: 10px;
  border-top-color: #878787;
  border-left-color: #878787;
  border-bottom-color: #fff;
  border-right-color: #fff;
  border-style: solid;
  border-width: 3px;
}

</style>