<template>
  <div class="container">
    <h1>Roulette</h1>
    <form @submit.prevent="submit">
      <div class="input-group mb-3"></div>
      <div>
        <input
          type="number"
          class="form-control mb-3"
          placeholder="Einsatz..."
          :value="inputEinsatz"
          required
        />
        <input
          type="number"
          class="form-control mb-3"
          placeholder="Höchsteinsatz..."
          :value="inputMaxValue"
          required
        />
        <button type="submit" class="btn btn-primary">Submit</button>
      </div>
      <p>Der Spieler gewinnt die Runde, wenn die Zahl zwischen 13 und 24 ist</p>
    </form>
    <table class="table table-striped table-hover">
      <thead>
        <tr>
          <th scope="col" class="text-center">#</th>
          <th scope="col" class="text-center">Winner Number</th>
          <th scope="col" class="text-center">Current Money</th>
          <th scope="col" class="text-center">Einsatz</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(current, index) in tries" :key="index">
          <th scope="row">{{ index }}</th>
          <td
            :class="
              current.winnerNumber >= 13 && current.winnerNumber <= 24
                ? 'text-center text-success'
                : 'text-center text-danger'
            "
          >
            {{ current.winnerNumber }}
          </td>
          <td
            :class="
              current.currentMoney >= 0
                ? 'text-center bg-success'
                : 'text-center bg-danger'
            "
          >
            {{ current.currentMoney }}
          </td>
          <td class="text-center">{{ current.einsatz }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      inputEinsatz: 5,
      inputMaxValue: 10000,
      einsatz: null,
      maxValue: null,
      tries: [],
    };
  },
  methods: {
    submit() {
      this.reset();
      this.einsatz = this.inputEinsatz;
      this.maxValue = this.inputMaxValue;
      for (var i = 0; i < 100; i++) {
        const win = this.getRandomInt();
        let currentMoney =
          this.tries.length > 1 ? this.tries[i - 1].currentMoney : this.einsatz;

        if (win >= 13 && win <= 24) {
          currentMoney = this.einsatz * 3;
          this.einsatz = this.inputEinsatz;
          currentMoney -= this.einsatz;
          this.tries.push({
            currentMoney: currentMoney,
            winnerNumber: win,
            einsatz: this.einsatz,
          });
        } else {
          // TODO: Aufhören bei Höchsteinsatz
          currentMoney -= this.einsatz;
          this.einsatz = this.einsatz * 2;
          this.tries.push({
            currentMoney: currentMoney,
            winnerNumber: win,
            einsatz: this.einsatz,
          });
        }
      }
    },
    reset() {
      this.tries = [];
      this.einsatz = this.inputEinsatz;
      this.maxValue = this.inputMaxValue;
    },

    getRandomInt() {
      return Math.floor(Math.random() * 36);
    },
  },
  mounted() {
    this.currentMoney = this.einsatz;
  },
};
</script>
