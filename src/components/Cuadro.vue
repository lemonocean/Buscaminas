<template>
  <div class="cuadro cuadro-vacio">
    <span :class="info.claseValor">{{ info.valor }}</span>
  </div>
</template>

<script>
export default {
  props: ['info']
}
</script>


<style>

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
