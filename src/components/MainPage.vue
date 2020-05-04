<template>
  <div class="main-page flex flex-col h-screen px-32">
    <div class="main-content flex flex-col justify-center items-center h-full">
      <div class="content flex flex-col text-center pb-16">
        <h1><i class="fas fa-glass-whiskey mr-4 purple-color"></i>Kielich</h1>
        <h3>Wirtualny alkomat</h3>
      </div>
      <div class="form w-2/3 flex flex-row justify-center items-center" @keyup.enter="calculate">
        <div class="flex w-1/5 mx-2">
          <input type="number" step="1" class="input" placeholder="Waga (kg)" v-model="weight" />
        </div>
        <div class="flex w-1/5 mx-2">
          <select class="input" v-model="sex">
            <option value="0.7">Mężczyzna</option>
            <option value="0.6">Kobieta</option>
          </select>
        </div>
        <div class="w-1/5 mx-2">
          <input type="number" step="1" class="input" placeholder="Ilość alkoholu (ml)" v-model="amountOfAlcohol" />
        </div>
        <div class="flex w-1/5 mx-2">
          <input type="number" step="1" class="input" placeholder="Moc alkoholu (%)" v-model="powerOfAlcohol" />
        </div>
        <div class="flex w-1/5 mx-2">
          <button class="btn" @click="calculate">Oblicz</button>
        </div>
      </div>
      <transition name="fade" mode="out-in">
        <div class="results w-2/3 pt-12 flex flex-row justify-center items-center" v-show="showResults">
          <div class="flex flex-col w-1/4">
            <h3>Orientacyjna ilość promili</h3>
            <h1>{{ amountAlcoholInBlood }}</h1>
          </div>
          <div class="flex flex-col w-1/4">
            <h3>Czysty alkohol (ml)</h3>
            <h1>{{ amountAlcoholInMilliliters }}</h1>
          </div>
          <div class="flex flex-col w-1/4">
            <h3>Czysty alkohol (g)</h3>
            <h1>{{ amountAlcoholInGrams }}</h1>
          </div>
          <div class="flex flex-col w-1/4">
            <h3>Szacunkowy czas trzeźwienia</h3>
            <h1 :style="estFontSize">{{ estimatedSobrietyTime }}</h1>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MainPage',
  data: () => ({
    weight: null,
    sex: 0.7,
    amountOfAlcohol: null,
    powerOfAlcohol: null,
    showResults: false,
    amountAlcoholInMilliliters: null,
    amountAlcoholInGrams: null,
    amountAlcoholInBlood: null,
    SJA: null,
    estimatedSobrietyTime: null,
    estFontSize: 'font-size: 50px'
  }),
  methods: {
    calculate: function () {
      this.amountAlcoholInMilliliters = this.amountOfAlcohol * (this.powerOfAlcohol / 100);
      this.amountAlcoholInGrams = this.amountAlcoholInMilliliters * 0.8;
      this.amountAlcoholInBlood = Math.round(this.amountAlcoholInGrams / (this.sex * this.weight) * 100) / 100;
      this.SJA = this.amountAlcoholInMilliliters / 12.5;

      // szacowany czas trzeźwienia
      var hours = this.SJA;
      var rhours = Math.floor(hours);
      var minutes = (hours - rhours) * 60;
      var rminutes = Math.round(minutes);
      if (rminutes) {
        this.estimatedSobrietyTime = rhours + ' godzin i ' + rminutes + ' minut';
      } else {
        this.estimatedSobrietyTime = rhours + ' godzin';
      }
      if (this.estimatedSobrietyTime.length > 10) {
        this.estFontSize = 'font-size: 24px';
      }
      this.showResults = true;
    }
  }
}
</script>

<style lang="scss" scoped>
  $purple: #8e44ad;
  body {
    font-family: 'Roboto', sans-serif;
  }
  .purple-color {
    color: $purple;
  }
  .main-page {
    .content {
      font-weight: 900;
      h1 {
        font-size: 50px;
      }
      h3 {
        font-size: 32px;
        text-transform: uppercase;
      }
    }
  }
  .form {
    .btn {
      width: 100%;
      background: $purple;
      border-radius: 3px;
      color: #fff;
      font-weight: 900;
      text-transform: uppercase;
      height: 36px;
    }
    .input {
      width: 100%;
      background: none;
      border-bottom: 2px solid $purple;
      border-radius: 3px;
      color: #7f8c8d;
      padding: 5px 10px;
      &::placeholder {
        color: #7f8c8d;
      }
    }
    input, select, button {
      outline: none;
    }
    select {
      height: 36px;
    }
  }
  .results {
    h3 {
      text-transform: uppercase;
      font-weight: 900;
      font-size: 14px;
    }
    h1 {
      color: $purple;
      font-weight: 900;
      font-size: 50px;
    }
  }
  .fade-enter-active {
    transition: .5s;
  }
  .fade-leave-active {
    transition: 1s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
