<template>
  <div class="cuadro cuadro-inicial">
    <span>{{ info.valor }}</span>
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

</style>
