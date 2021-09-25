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
          v-model="einsatz"
          required
        />
        <input
          type="number"
          class="form-control mb-3"
          placeholder="Höchsteinsatz..."
          v-model="maxValue"
          required
        />
        <button type="submit" class="btn btn-primary">Submit</button>
      </div>
    </form>
    <table class="table">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Winner Number</th>
          <th scope="col">Current Money</th>
          <th scope="col">Handle</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(number, index) in winnerNumber" :key="index">
          <th scope="row">{{ index }}</th>
          <td>{{ number.winner }}</td>
          <td>{{ number.currentValue }}</td>
          <td>@mdo</td>
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
      einsatz: null,
      maxValue: null,
      winnerNumber: [],
    };
  },
  methods: {
    submit() {
      console.log(this.einsatz);
      console.log(this.maxValue);
      for (var i = 0; i < 100; i++) {
        const randomVal = this.getRandomInt();
        this.winnerNumber.push({
          winner: randomVal,
          currentValue: (this.maxValue -= this.einsatz),
        });
        this.einsatz = this.einsatz * 2;
      }
    },

    getRandomInt() {
      return Math.floor(Math.random() * 36);
    },
  },
};
</script>

<style></style>
