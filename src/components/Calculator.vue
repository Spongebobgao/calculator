<template>
  <div>
    <h2>Lin's Calculator</h2>
    <div class="calculator">
      <div class="totoal">
        <div>{{current}}</div>
      </div>
      <div
        v-for="(btn,index) of btnInfo"
        :class="`${btn.digit?'btn':'btn special'}`"
        :style="`${index===16?'width:190px':''}`"
        :key="btn.value"
        @click="`${btn.digit?appendNum(btn.value):getOperator(btn.value)}`"
      >
        <span>{{btn.value}}</span>
      </div>
    </div>
  </div>
</template>

<script>
import btnInfo from "../../btnInfo";
export default {
  name: "Calculator",
  data() {
    return {
      current: 0,
      previous: 0,
      decimalPlaces: 0,
      temp: 0,
      newCurrent: false,
      operator: "",
      btnInfo: btnInfo
    };
  },
  methods: {
    appendNum(number) {
      if (this.newCurrent) {
        this.current = number;
        this.newCurrent = false;
      } else if (this.decimalPlaces > 0) {
        this.current = parseFloat(this.current);
        this.current =
          this.current < 0
            ? this.current - number / Math.pow(10, this.decimalPlaces)
            : this.current + number / Math.pow(10, this.decimalPlaces);
        this.decimalPlaces++;
      } else {
        this.current =
          this.current < 0
            ? this.current * 10 - number
            : this.current * 10 + number;
      }
    },
    getOperator(operator) {
      switch (operator) {
        case ".":
          if (this.current.toString().indexOf(".") === -1)
            this.current = this.current.toString() + ".";
          this.decimalPlaces = 1;
          break;
        case "AC":
          this.current = this.previous = this.decimalPlaces = 0;
          this.newCurrent = false;
          this.operator = "";
          break;
        case "+/-":
          this.current = -this.current;
          break;
        case "%":
          this.current = this.current / 100;
          this.decimalPlaces = 0;
          this.newCurrent = true;
          break;
        case "+":
        case "-":
        case "x":
        case "/":
          this.decimalPlaces = 0;
          if (this.operator === "" || this.newCurrent) {
            this.previous = this.current;
            this.operator = operator;
            this.newCurrent = true;
          } else {
            this.calculate(operator);
          }
          break;
        case "=":
          this.newCurrent = true;
          this.decimalPlaces = 0;
          if (this.newCurrent) {
            this.calculateItself();
          } else {
            this.calculate(operator);
          }
          break;
      }
    },
    calculate(operator) {
      this.newCurrent = true;
      switch (this.operator) {
        case "+":
          this.current = this.previous = this.previous + this.current;
          this.operator = operator === "=" ? "" : operator;
          break;
        case "-":
          this.current = this.previous = this.previous - this.current;
          this.operator = operator === "=" ? "" : operator;
          break;
        case "x":
          this.current = this.previous = this.previous * this.current;
          this.operator = operator === "=" ? "" : operator;
          break;
        case "/":
          if (this.current === 0) {
            this.prevoius = this.current = 0;
            this.operator = "";
          } else {
            this.current = this.previous = this.previous / this.current;
            this.operator = operator === "=" ? "" : operator;
          }
          break;
      }
    },
    calculateItself() {
      switch (this.operator) {
        case "+":
          this.current = this.current + this.previous;
          break;
        case "-":
          this.current = this.previous - this.current;
          break;
        case "x":
          this.current = this.current * this.previous;
          break;
        case "/":
          if (this.current === 0) {
            this.prevoius = this.current = 0;
          } else {
            this.current = this.previous / this.current;
          }
          break;
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.calculator {
  width: 400px;
  height: 450px;
  font-size: 1.5rem;
  display: flex;
  flex-wrap: wrap;
  background: black;
  color: white;
  padding: 10px;
  margin: auto;
  border-radius: 5%;
}
.calculator > div {
  display: flex;
}
.calculator :hover {
  color: black;
  background: white;
}
.totoal {
  width: 95%;
  font-size: 33px;
  margin: 0 2.5%;
  justify-content: flex-end;
  padding: 10px 0;
}

.btn {
  width: 90px;
  height: 60px;
  background: grey;
  border-radius: 20%;
  padding: 0;
  cursor: pointer;
  margin: 5px;
  justify-content: center;
}
.btn > span {
  align-self: center;
}
.special {
  background: orange;
}
</style>
