<template>
  <div>
    <h3>For keyboard input, please press C to clear and S for +/-</h3>
    <div class="calculator">
      <div class="total">
        <div>{{current}}</div>
      </div>
      <button
        v-for="(btn,index) of btnInfo"
        :id="`${index}`"
        :class="`${btn.digit?'btn':'btn special'}`"
        :style="`${index===16?'width:190px':''}`"
        :key="btn.value"
        @click="`${btn.digit?appendNum(btn.value):getOperator(btn.value)}`"
      >{{btn.value}}</button>
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
  created() {
    window.addEventListener("keypress", this.keyPressEvent);
  },
  methods: {
    keyPressEvent(e) {
      var valueEntered = e.key;
      const digit = {
        "1": 1,
        "2": 2,
        "3": 3,
        "4": 4,
        "5": 5,
        "6": 6,
        "7": 7,
        "8": 8,
        "9": 9,
        "0": 0
      };
      const operator = {
        "+": "+",
        "-": "-",
        x: "x",
        X: "x",
        "/": "/",
        "=": "=",
        ".": ".",
        C: "AC",
        c: "AC",
        "%": "%",
        S: "+/-",
        s: "+/-",
        "*": "x"
      };
      if (digit[valueEntered] !== undefined) {
        var index = btnInfo.findIndex(x => x.value === digit[valueEntered]);
        this.setStyle(index);
        this.appendNum(digit[valueEntered]);
      } else if (operator[valueEntered]) {
        index = btnInfo.findIndex(x => x.value === operator[valueEntered]);
        this.setStyle(index);
        this.getOperator(operator[valueEntered]);
      }
    },
    setStyle(index) {
      var element = document.getElementById(index);
      element.classList.add("keyBoard");
      setTimeout(function() {
        element.classList.remove("keyBoard");
      }, 200);
    },
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
          if (this.current.toString().indexOf(".") === -1) {
            this.current = this.current.toString() + ".";
            this.decimalPlaces = 1;
          }
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
          this.decimalPlaces = 0;
          if (this.newCurrent) {
            this.calculateItself();
          } else {
            this.newCurrent = true;
            this.calculate(operator);
          }
          break;
      }
    },
    calculate(operator) {
      this.newCurrent = true;
      var temp = this.current;
      var currentDecimal =
        this.current.toString().indexOf(".") === -1
          ? 0
          : this.current.toString().split(".")[1].length;
      var previousDecimal =
        this.previous.toString().indexOf(".") === -1
          ? 0
          : this.previous.toString().split(".")[1].length;
      switch (this.operator) {
        case "+":
          this.current = this.previous + this.current;
          this.formatCurrent(this.current, currentDecimal, previousDecimal);
          this.previous = operator === "=" ? temp : this.current;
          this.operator = operator === "=" ? this.operator : operator;
          break;
        case "-":
          this.current = this.previous - this.current;
          this.formatCurrent(this.current, currentDecimal, previousDecimal);
          this.previous = operator === "=" ? temp : this.current;
          this.operator = operator === "=" ? this.operator : operator;
          break;
        case "x":
          this.current = this.previous * this.current;
          this.current = parseFloat(
            this.current.toFixed(currentDecimal + previousDecimal)
          );
          this.previous = operator === "=" ? temp : this.current;
          this.operator = operator === "=" ? this.operator : operator;
          break;
        case "/":
          if (this.current === 0) {
            this.prevoius = this.current = 0;
            this.operator = "";
          } else {
            this.current = this.previous / this.current;
            this.previous = operator === "=" ? temp : this.current;
            this.operator = operator === "=" ? this.operator : operator;
          }
          break;
      }
    },
    calculateItself() {
      var indexCurrent =
        this.current.toString().indexOf(".") === -1
          ? 0
          : this.current.toString().split(".")[1].length;
      var indexPrevious =
        this.previous.toString().indexOf(".") === -1
          ? 0
          : this.previous.toString().split(".")[1].length;
      switch (this.operator) {
        case "+":
          this.current = this.current + this.previous;
          this.formatCurrent(this.current, indexCurrent, indexPrevious);
          break;
        case "-":
          this.current = this.current - this.previous;
          this.formatCurrent(this.current, indexCurrent, indexPrevious);
          break;
        case "x":
          this.current = this.current * this.previous;
          this.current = parseFloat(
            this.current.toFixed(indexPrevious + indexCurrent)
          );
          break;
        case "/":
          if (this.current === 0) {
            this.prevoius = this.current = 0;
          } else {
            this.current = this.current / this.previous;
          }
          break;
      }
    },
    formatCurrent(current, currentDecimal, previousDecimal) {
      this.current = parseFloat(
        this.current.toFixed(
          currentDecimal >= previousDecimal ? currentDecimal : previousDecimal
        )
      );
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.calculator {
  width: 400px;
  height: 450px;
  display: flex;
  flex-wrap: wrap;
  background: black;
  color: white;
  padding: 10px;
  margin: auto;
  border-radius: 5%;
}
.calculator :hover {
  color: black;
  background: white;
}

.total {
  width: 95%;
  font-size: 33px;
  margin: 0 2.5%;
  display: flex;
  justify-content: flex-end;
  padding: 10px 0;
}
button {
  outline: none;
  border: none;
  color: white;
}
.btn {
  width: 90px;
  height: 60px;
  background: grey;
  border-radius: 20%;
  font-size: 1.5rem;
  padding: 0;
  cursor: pointer;
  margin: 5px;
  justify-content: center;
}
.special {
  background: orange;
}
.keyBoard {
  background: white;
  color: black;
}
</style>
