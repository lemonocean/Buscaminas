<template>
  <div class="tablero">
    <div class="niveles">
      <span>Nivel:</span>
      <span class="nivel">1</span>
      <span class="nivel">2</span>
      <span class="nivel">3</span>
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

    </div>
  </div>
</template>

<style>

.tablero {
  display: grid;
  justify-content: center;
  background-color: #bdbdbd;
  padding: 10px;
}

.niveles {
  display: grid;
  grid-auto-flow: column;
}

.nivel {
  width: 32px;
  height: 32px;
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
  width: 400px;
  height: 200px;
}

</style>
