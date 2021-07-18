<template>
  <div class="calculator">

    <!-- I kept these lines in to make debugging easier in the future
    <div class="display">Current: {{ current }}</div>
    <div class="display">Previous: {{ previous }}</div>
    <div class="display">Total: {{ total }}</div> -->

    <div class="display" v-if="!showResult">{{ display || 0 }}</div>
    <div class="display" v-else >{{ finalDisplay || 0 }}</div>
    
    <div @click="handleDiceClick(4)" class="btn">d4</div>
    <div @click="handleDiceClick(6)" class="btn">d6</div>
    <div @click="handleDiceClick(8)" class="btn">d8</div>
    <div @click="clear" class="btn" id="clearButton">AC</div>

    <div @click="handleDiceClick(10)" class="btn">d10</div>
    <div @click="handleDiceClick(12)" class="btn">d12</div>
    <div @click="handleDiceClick(20)" class="btn">d20</div>
    <div @click="handleOperatorClick('/')" class="btn operator">/</div>

    <div @click="handleDigitClick('7')" class="btn">7</div>
    <div @click="handleDigitClick('8')" class="btn">8</div>
    <div @click="handleDigitClick('9')" class="btn">9</div>
    <div @click="handleOperatorClick('x')" class="btn operator">x</div>

    <div @click="handleDigitClick('4')" class="btn">4</div>
    <div @click="handleDigitClick('5')" class="btn">5</div>
    <div @click="handleDigitClick('6')" class="btn">6</div>
    <div @click="handleOperatorClick('-')" class="btn operator">-</div>

    <div @click="handleDigitClick('1')" class="btn">1</div>
    <div @click="handleDigitClick('2')" class="btn">2</div>
    <div @click="handleDigitClick('3')" class="btn">3</div>
    <div @click="handleOperatorClick('+')" class="btn operator">+</div>

    <div @click="handleDigitClick('0')" class="btn zero">0</div>
    <div @click="dot" class="btn">.</div>
    <div @click="equal" class="btn operator">Roll</div>
    
  </div>
</template>

<script>
export default {
  data () {
    return {
      display: '',
      finalDisplay: '',
      current: '',
      total: null,
      previous: null,
      operator: null,
      operatorClicked: false,
      isDiceValidChoice: true,
      isDigitValidChoice: true,
      showRolls: false,
      showResult: false,
    }
  },
  methods: {
    clear () {
      this.current = '';
      this.display = '';
      this.total = null;
      this.previous = null;
      this.finalDisplay = '';
      this.isDiceValidChoice = false;
      this.isDigitValidChoice = true;
      this.showRolls = false;
      this.showResult = false;
    },
    handleDigitClick(number) {
      if(this.isDigitValidChoice) {
        this.append(number)
      }
      this.isDiceValidChoice = true
    },
    append(number) {
      if(this.operatorClicked){
        
        this.current = ''
        this.operatorClicked = false
      }
      this.current = `${this.current}${number}`
      this.display += number
      this.finalDisplay += number
    },
    dot() {
      if(this.current.indexOf('.') === -1) {
        this.append('.');
      }
    },
    handleOperatorClick(operator) {
      if (this.current) {
        this.previous = this.current
        this.current = ''
      }
      this.display += operator;
      this.finalDisplay += operator;
      this.operator = operator;

      this.isDigitValidChoice = true;
      this.isDiceValidChoice = false;
    },
    equal() {
      let p = parseInt(this.previous);
      let c = 0;
     
     if (this.current) {
        c = parseInt(this.current);
      }
      
      switch (this.operator) {
        case '/':
          this.total = p / c
          break;
        case '*':
          this.total = p * c
          break;
        case '+':
          this.total = p + c
          break;
        case '-':
          this.total = p - c
          break;
        default:
          break;
      }
      this.display += " = " + this.total;
      this.finalDisplay += " = " + this.total;
      this.showRolls = true;
      this.showResult = true;
    },
    rollDice(dice) {
      let roll = Math.ceil(Math.random() * dice);
      console.log("d" + dice + ": " + roll);
      return roll;
    },
    rollMultipleDice(size, amount) {
      let rolls = []
      let sum = 0;

      for(let i = 0; i < parseInt(amount); i++){
        let roll = this.rollDice(size);
        rolls.push(roll);
        sum += roll;
      }

      this.finalDisplay += `(${rolls.join('+')})`
      return sum;
    },
    handleDiceClick(size) {
      if(this.isDiceValidChoice){
        this.display += `d${size}`
        this.finalDisplay += `d${size}`
        this.isDigitValidChoice = false
        this.isDiceValidChoice = false

        let currentRoll = this.rollMultipleDice(size, this.current);
        console.log(`${this.current}d${size}: ${currentRoll}`);
        this.total += currentRoll;
        this.previous = currentRoll;
        this.current = '';
      }
    }
  }
}
</script>

<style>
.calculator {
  position:absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  margin: 0px;
  font-size: 40px;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-auto-rows: minmax(50px, auto);
  cursor: pointer;
}
#clearButton{
  background-color:#C53420
}
.display {
  grid-column: 1 / 5;
  background-color: #333;
  color: white;
  border: 1px solid black;

}
.zero {
  grid-column: 1/3;
}
.btn {
  background-color: #999;
  border: 1px solid black;
}
.operator {
  background-color: orange;
  color: white;
}
</style>