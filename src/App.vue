<template>
  <div id="app">
    <h1>Znansveni radovi</h1>
    <p>Prosjecna starost radova : {{ prosjekGodina }}</p>
    <button class="btn" v-on:click="obrisiOdabraneRadove()">
      Obrisi odabrane radove
    </button>
    <div>
      <button class="btn" :disabled="isDisabled" v-on:click="moveUp()">
        Move up +
      </button>
      <button class="btn" :disabled="isDisabled" v-on:click="moveDown()">
        Move down -
      </button>
    </div>
    <List
      :radovi="this.znanstveniRadovi"
      :odabraniRadovi="odabraniRadovi"
      :znanstveniRadovi="znanstveniRadovi"
    />
  </div>
</template>

<script>
import List from "./components/List.vue";
import axios from "axios";
import {
  differentialOfArrays,
  sortiranePoStarosti,
  averageYears,
} from "./utils";
export default {
  name: "App",
  components: {
    List,
  },
  data() {
    return {
      znanstveniRadovi: [],
      znanstveniRadoviProvjera: [],
      prosjekGodina: null,
      odabraniRadovi: [],
      isDisabled: true,
      obrisiOdabraneRadove() {
        const noviRadovi = this.znanstveniRadovi.filter(
          (rad) => !this.odabraniRadovi.includes(rad)
        );
        this.znanstveniRadovi = noviRadovi;
      },
      moveUp() {
        const from = this.znanstveniRadovi.indexOf(this.odabraniRadovi[0]);
        let to = from + 1;

        if (from != this.znanstveniRadovi.length - 1) {
          this.znanstveniRadovi.splice(
            to,
            0,
            this.znanstveniRadovi.splice(from, 1)[0]
          );
        } else {
          to = 0;
          this.znanstveniRadovi.splice(
            to,
            0,
            this.znanstveniRadovi.splice(from, 1)[0]
          );
        }
      },
      moveDown() {
        const from = this.znanstveniRadovi.indexOf(this.odabraniRadovi[0]);
        let to = from - 1;

        if (from != 0) {
          this.znanstveniRadovi.splice(
            to,
            0,
            this.znanstveniRadovi.splice(from, 1)[0]
          );
        } else {
          to = this.znanstveniRadovi.length - 1;
          this.znanstveniRadovi.splice(
            to,
            0,
            this.znanstveniRadovi.splice(from, 1)[0]
          );
        }
      },
    };
  },
  created() {
    const requestOne = axios.get(
      "https://mocki.io/v1/53083792-ee35-4ec5-bebb-44e802bd62a0"
    );
    const requestTwo = axios.get(
      "https://mocki.io/v1/45d27447-2b84-4355-bc89-7758b6498d78"
    );
    axios.all([requestOne, requestTwo]).then(
      axios.spread((...responses) => {
        const responseOne = responses[0];
        const responseTwo = responses[1];
        (this.znanstveniRadovi = responseOne.data),
          (this.znanstveniRadoviProvjera = responseTwo.data);
        this.znanstveniRadovi = sortiranePoStarosti(
          differentialOfArrays(responseOne.data, responseTwo.data)
        );
        this.prosjekGodina = averageYears(this.znanstveniRadovi);
      })
    );
  },

  watch: {
    "odabraniRadovi.length"() {
      this.isDisabled = this.odabraniRadovi.length == 1 ? false : true;
    },
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
  margin-top: 60px;
}
.btn {
  padding: 10px;
  border: none;
  font-size: 15px;
  font-weight: 500;
  background-color: rgb(122, 230, 122);
  margin-right: 10px;
  margin-top: 10px;
}
</style>
