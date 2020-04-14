<template>
  <div>
    <h3>Lin's Calculator</h3>
    <div class="calculator">
    <div class="totoal">{{current || 0}}</div>
    <div @click="clear" class="btn special">AC</div>
    <div class="btn special">+/-</div>
    <div class="btn special">%</div>
    <div class="btn special">/</div>
    <div class="btn">7</div>
    <div class="btn">8</div>
    <div class="btn">9</div>
    <div class="btn special">x</div>
    <div class="btn">4</div>
    <div class="btn">5</div>
    <div class="btn">6</div>
    <div class="btn special">-</div>
    <div class="btn">1</div>
    <div class="btn">2</div>
    <div class="btn">3</div>
    <div class="btn special">+</div>
    <div class="btn zero">0</div>
    <div class="btn">.</div>
    <div class="btn special">=</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Calculator',
  data(){
    return {
      current: ''
    }
  },
  methods: {
    clear(){
      this.current = ''
    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px;
}
.calculator {
  width:450px;
  height:450px;
  display: grid;
  grid-gap: 30px;
  grid-template-columns: repeat(4,1fr);
  grid-template-rows: auto;
  background:black;
  color:white;
  padding:10px;
  margin:auto;
  border-radius: 5%;
}
.totoal {
  grid-column: 1/5;
  font-size: 3rem;
}

.btn {
  width:40px;
  height:40px;
  border-radius: 50%;
  background: grey;
  padding:0;
  cursor: pointer;
}
.zero {
  grid-column: 1/3;
}
.special {
  background: orange;
}
</style>
