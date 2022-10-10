<template>
  <div class="w-screen max-w-sm text-center bg-light border border-dark rounded rounded-sm" style="padding: 15px; margin: 0 auto">
    <table class="w-full">
      <tr>
        <th>x<sub>i</sub></th>
        <th>f(x<sub>i</sub>)</th>
      </tr>
      <tr v-for="i in (n + 1)" v-bind:key="i">
        <td><input type="text" name="x{{(i-1)}}" id="x{{(i-1)}}" v-model.number="xs[(i-1)]"></td>
        <td><input type="text" name="f{{(i-1)}}" id="f{{(i-1)}}" v-model.number="fs[(i-1)]"></td>
      </tr>
    </table>
    <br>
    f(<input type="text" name="x" id="x" v-model.number="x" placeholder="nuevo valor">) = <input type="text" name="result" id="result" v-model.number="result" placeholder="resultado">
    <br>
    <button v-on:click="increase()">Agregar valor</button>
    <button v-on:click="decrease()">Eliminar valor</button>
    <button v-on:click="solve()">Calcular</button>
  </div>
</template>

<script>
import { create, all } from "mathjs";

const math = create(all, {});                      

export default {
  name: "App",
  components: {},
  data() {
    return {
      n: 0,
      xs: [],
      fs: [],
      cs: [],
      x: undefined,
      differences: [],
      h: undefined,
      result: 0,
    };
  },
  methods: {
    increase() {
      this.n += 1;
      this.xs.push(null);
      this.fs.push(null);
      this.differences.push(null);
      console.log(this.xs, this.fs)
    },
    decrease() {
      this.n -= 1;
      this.xs.pop();
      this.fs.pop();
      this.differences.pop();
      console.log(this.xs, this.fs)
    },
    get_difference() {
      const differences = Array.from(new Set(this.xs.slice(1).map((n, i) => parseFloat((n - this.xs[i]).toFixed(6)))));
      console.log(differences)
      if (differences.every((val, i, arr) => val === arr[0])) {
        this.h = differences[0]
        console.log(this.h)
      } else {
        alert("No estan igualmente espaciados")
      }
    },
    get_differences(list) {
      return list.slice(1).map((n, i) => parseFloat((n - list[i]).toFixed(6)));
    },
    solve() {
      this.cs = [];
      this.result = 0;
      this.get_difference();
      this.differences[0] = this.get_differences(this.fs)
      this.cs.push(this.fs[0])
      for (let index = 1; index < this.n; index++) {
        this.differences[index] = this.get_differences(this.differences[index-1])
        console.log(this.differences[index][0] + "/" + "(" + math.factorial(index) + "*" + (this.h ** index) + ")")
        this.cs.push(this.differences[index - 1][0] / (math.factorial(index) * (this.h ** index)))
      }
      console.log(this.differences[this.n - 1][0] + "/" + "(" + math.factorial(this.n) + "*" + (this.h ** this.n) + ")")
      this.cs.push(this.differences[this.n -1 ][0] / (math.factorial(this.n) * (this.h ** this.n)))
      console.log(this.differences, this.cs);

      console.log(this.x)
      for (let index = 0; index <= this.n; index++) {
        let mult = 1;
        for (let j = 0; j < index; j++) {
          console.log("mult *= (" + this.x + "-" + this.xs[j] + ")")
          mult *= (this.x - this.xs[j])      
        }
        this.result += this.cs[index] * mult  
      }
      this.result = this.result.toFixed(6)
      console.log(this.result)
    }
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px;
}
</style>
