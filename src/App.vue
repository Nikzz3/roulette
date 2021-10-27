<template>
  <div class="container">
    <h1>Roulette Robin Scherf und Nick Zimmermann</h1>
    <b-form-checkbox v-model="checked" name="check-button" switch>
      Analyse
    </b-form-checkbox>

    <form @submit.prevent="checked ? analyse() : submit()">
      <div class="input-group mb-3"></div>
      <div>
        <label>Einsatz</label>
        <input
          type="number"
          class="form-control mb-3"
          placeholder="Einsatz..."
          v-model="inputEinsatz"
          required
        />
        <label>Höchsteinsatz</label>
        <input
          type="number"
          class="form-control mb-3"
          placeholder="Höchsteinsatz..."
          v-model="inputMaxValue"
          required
        />
        <label v-if="checked">Anzahl Durchläufe</label>
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
      <div v-if="tries.length > 0">
        <h3
          v-if="tries[tries.length - 1].currentMoney > 0"
          class="text-success"
        >
          Gewonnen!
        </h3>
        <h3 v-else class="text-danger">Verloren!</h3>
        <p>Verloren: {{ losses }}</p>
        <p>Gewonnen: {{ wins }}</p>
      </div>
      <table class="table table-striped table-hover">
        <thead>
          <tr>
            <th scope="col" class="text-center">Durchläufe Anzahl</th>
            <th scope="col" class="text-center">Gewinnerzahl</th>
            <th scope="col" class="text-center">Einsatz</th>
            <th scope="col" class="text-center">Aktuelles Guthaben</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(current, index) in tries" :key="index">
            <th scope="row">{{ index + 1 }}</th>
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
      inputMaxValue: 1000,
      einsatz: null,
      maxValue: null,
      tries: [],
      winnerNumber: 0,
      checked: false,
      losses: 0,
      wins: 0,
      rounds: 1000,
    };
  },
  methods: {
    analyse() {
      this.reset();
      this.wins = 0;
      this.losses = 0;
      this.einsatz = this.inputEinsatz;
      this.maxValue = this.inputMaxValue;
      for (let j = 0; j < this.rounds; j++) {
        this.calc();
        this.reset();
      }
    },
    calc() {
      for (var i = 0; i < 100; i++) {
        this.winnerNumber = this.getRandomInt(0, 36);
        let currentMoney =
          this.tries.length > 0 ? this.tries[i - 1].currentMoney : 0;

        if (this.winnerNumber >= 13 && this.winnerNumber <= 24) {
          this.addItem(this.win(currentMoney));
          this.einsatz = this.inputEinsatz;
        } else if (this.winnerNumber === 0) {
          this.addItem(currentMoney);
        } else {
          const newItem = this.loss(currentMoney);
          if (this.einsatz > this.inputMaxValue) {
            this.einsatz = this.inputEinsatz;
            break;
          }
          this.addItem(newItem);
          this.einsatz *= 2;
        }
      }
      if (this.tries[this.tries.length - 1].currentMoney > 0) {
        this.wins++;
      } else {
        this.losses++;
      }
    },
    submit() {
      this.reset();
      this.einsatz = this.inputEinsatz;
      this.maxValue = this.inputMaxValue;
      this.calc();
    },
    win(currentMoney) {
      currentMoney += this.einsatz * 2;
      return currentMoney;
    },
    loss(currentMoney) {
      currentMoney -= this.einsatz;
      return currentMoney;
    },
    reset() {
      this.tries = [];
      this.einsatz = this.inputEinsatz;
      this.maxValue = this.inputMaxValue;
    },
    getRandomInt(min, max) {
      min = Math.ceil(min);
      max = Math.floor(max);
      return Math.floor(Math.random() * (max - min + 1)) + min;
    },
    addItem(currentMoney) {
      this.tries.push({
        currentMoney: currentMoney,
        winnerNumber: this.winnerNumber,
        einsatz: this.einsatz,
      });
    },
  },
  watch: {
    checked() {
      this.wins = 0;
      this.losses = 0;
    },
  },
};
</script>
