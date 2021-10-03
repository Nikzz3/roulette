<template>
  <div class="container">
    <h1>Roulette</h1>
    <b-form-checkbox v-model="checked" name="check-button" switch>
      Analyse
    </b-form-checkbox>

    <form @submit.prevent="checked ? analyse() : submit()">
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
        <input
          type="number"
          class="form-control mb-3"
          placeholder="Anzahl Durchläufe"
          v-model="rounds"
          required
          v-if="checked"
        />
        <button type="submit" class="btn btn-primary">Speichern</button>
      </div>
      <p>
        Der Spieler gewinnt die Runde, wenn die Zahl zwischen 13 und 24 ist.
      </p>
    </form>
    <div v-if="!checked">
      <table class="table table-striped table-hover">
        <thead>
          <tr>
            <th scope="col" class="text-center">#</th>
            <th scope="col" class="text-center">Gewinnerzahl</th>
            <th scope="col" class="text-center">Einsatz</th>
            <th scope="col" class="text-center">Aktuelles Guthaben</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(current, index) in tries" :key="index">
            <th scope="row">{{ index }}</th>
            <td
              :class="
                current.winnerNumber >= 13 && current.winnerNumber <= 24
                  ? 'text-center bg-success'
                  : 'text-center bg-danger'
              "
            >
              {{ current.winnerNumber }}
            </td>
            <td class="text-center">{{ current.einsatz }}</td>
            <td
              :class="
                current.currentMoney >= 0
                  ? 'text-center bg-success'
                  : 'text-center bg-danger'
              "
            >
              {{ current.currentMoney }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div v-else>
      <p>Verloren: {{ losses }}</p>
      <p>Gewonnen: {{ wins }}</p>
    </div>
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
      winnerNumber: 0,
      checked: false,
      losses: 0,
      wins: 0,
      rounds: 10,
    };
  },
  methods: {
    analyse() {
      this.reset();
      this.wins = 0;
      this.losses = 0;
      this.einsatz = this.inputEinsatz;
      this.maxValue = this.inputMaxValue;
      console.log(this.rounds);
      for (let j = 0; j < this.rounds; j++) {
        for (var i = 0; i < 100; i++) {
          this.winnerNumber = this.getRandomInt();
          let currentMoney =
            this.tries.length > 0 ? this.tries[i - 1].currentMoney : 0;

          if (this.winnerNumber >= 13 && this.winnerNumber <= 24) {
            this.addItem(this.win(currentMoney));
          } else if (this.winnerNumber === 0) {
            this.addItem(currentMoney);
          } else {
            this.addItem(this.loss(currentMoney));
          }
          if (this.einsatz > this.inputMaxValue) {
            break;
          }
        }
        if (this.tries[this.tries.length - 1].currentMoney > 0) {
          this.wins++;
        } else {
          this.losses++;
        }
        this.tries = [];
      }
    },
    submit() {
      this.reset();
      this.einsatz = this.inputEinsatz;
      this.maxValue = this.inputMaxValue;
      for (var i = 0; i < 100; i++) {
        this.winnerNumber = this.getRandomInt();
        let currentMoney =
          this.tries.length > 0 ? this.tries[i - 1].currentMoney : 0;

        if (this.winnerNumber >= 13 && this.winnerNumber <= 24) {
          this.addItem(this.win(currentMoney));
        } else if (this.winnerNumber === 0) {
          this.addItem(currentMoney);
        } else {
          this.addItem(this.loss(currentMoney));
        }
        if (this.einsatz > this.inputMaxValue) {
          break;
        }
      }
    },
    win(currentMoney) {
      currentMoney += this.einsatz * 2;
      this.einsatz = this.inputEinsatz;
      return currentMoney;
    },
    loss(currentMoney) {
      currentMoney -= this.einsatz;
      this.einsatz *= 2;
      return currentMoney;
    },
    reset() {
      this.tries = [];
      this.einsatz = this.inputEinsatz;
      this.maxValue = this.inputMaxValue;
    },
    getRandomInt() {
      return Math.floor(Math.random() * 36);
    },
    addItem(currentMoney) {
      this.tries.push({
        currentMoney: currentMoney,
        winnerNumber: this.winnerNumber,
        einsatz: this.einsatz,
      });
    },
  },
};
</script>
