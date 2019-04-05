<template>
  <div 
    @mousedown.right="bandera"
    @mouseup.left="activar"
    @contextmenu.prevent
    class="cuadro" :class="info.inicial ? 'cuadro-inicial': 'cuadro-vacio'">
    <span :class="clase">{{ valor }}</span>
  </div>
</template>

<script>
export default {
  props: ['info'],
  computed: {
    valor () {
      if (this.info.inicial) {
        if (this.info.bandera) {
          return '🚩'
        }
        else {
          return ''
        }
      }
      else {
        return this.info.valor
      }
    },
    clase () {
      if (this.info.inicial) {
        if (this.info.bandera) {
          return 'bandera'
        }
        else {
          return ''
        }
      }
      else {
        return this.info.claseValor
      }
    }
  },
  methods: {
    bandera () {
      if (this.info.inicial) {
        this.info.bandera = !this.info.bandera
        this.$emit('onCambiarMinasRestantes', this.info.bandera ? -1 : 1)
      }
    },
    activar () {
      this.$emit('onActivar', this.info)
    }
  }
}
</script>


<style>

.bandera {
  font-size: 12px;
}

.cuadro {
  display: grid;
  justify-items: center;
  align-items: center;
}

.cuadro-inicial {
  width: 20px;
  height: 20px;
  background-color: #bdbdbd;
  border-top-color: #fff;
  border-left-color: #fff;
  border-right-color: #818181;
  border-bottom-color: #818181;
  border-style: solid;
  border-width: 3px;
  cursor: pointer;
}

.cuadro-vacio {
  width: 25px;
  height: 25px;
  background-color: #bdbdbd;
  border-right-color: #7b7b7b;
  border-bottom-color: #7b7b7b;
  border-top-width: 0;
  border-left-width: 0;
  border-bottom-width: 1px;
  border-right-width: 1px;
  border-style: solid;
  cursor: default;
}

</style>
