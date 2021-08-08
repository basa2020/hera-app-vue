<template>
  <div
    :class="[isSelected == true ? 'red_border' : 'grey_border']"
    v-on:click="odaberiRad(rad)"
  >
    <h1>{{ rad.autor }}-{{ rad.naziv }}</h1>
    <span>Starost : {{ starost }}</span>
    <p
      v-for="zanr of zanrovi"
      :key="zanr"
      :class="[zanr === zanrKojeSeNajvisePonavlja ? 'red_color' : '']"
    >
      Zanr : {{ zanr }}
    </p>
    <p v-for="najduziZanr of najduziZanrovi" :key="najduziZanr.naziv">
      NajduziZanr : {{ najduziZanr.naziv }} , Duzina :
      {{ najduziZanr.naziv.length }}
    </p>
    <button class="btn" v-on:click="izbrisiElement(rad)">Izbrisi Rad</button>
  </div>
</template>

<script>
import { sortiranjeZanr } from "../utils.js";
export default {
  name: "SingleList",
  props: [
    "rad",
    "odabraniRadovi",
    "znanstveniRadovi",
    "zanrKojeSeNajvisePonavlja",
  ],
  data() {
    return {
      isSelected: false,
      zanrovi: [],
      starost: [],
      najduziZanrovi: [],
      odaberiRad(rad) {
        this.isSelected = !this.isSelected;
        const postoji = this.odabraniRadovi.find((p) => p.id === rad.id);
        if (this.isSelected && !postoji) {
          this.odabraniRadovi.push(rad);
        } else if (!this.isSelected && postoji) {
          const id = this.znanstveniRadovi.indexOf(rad.id);

          this.odabraniRadovi.splice(id, 1);
        }
      },
      izbrisiElement(rad) {
        const id = this.znanstveniRadovi.indexOf(rad);

        this.znanstveniRadovi.splice(id, 1);
      },
      duzinaZanrova(zanrovi) {
        zanrovi.forEach((element) => {
          if (element.naziv.length > 16) {
            this.najduziZanrovi.push(element);
          }
          if (element.podZanrovi) {
            element.podZanrovi.forEach((item) => {
              if (item.naziv.length > 16) {
                this.najduziZanrovi.push(item);
              }
              this.duzinaZanrova(item.podZanrovi);
            });
          }
        });
      },
    };
  },
  created() {
    this.zanrovi = sortiranjeZanr(this.rad.zanr);
    const { datumIzdanja } = this.rad.datum;
    var godinaIzdanja = datumIzdanja.substr(datumIzdanja.length - 4);
    const now = new Date().getFullYear();

    this.starost = now - godinaIzdanja;

    this.duzinaZanrova(this.rad.podZanrovi);
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.red_border {
  border: 5px solid red;
  width: 65vw;
  margin: auto;
  margin-top: 10px;
  padding: 10px;
}
.grey_border {
  border: 5px solid grey;
  width: 65vw;
  margin: auto;
  margin-top: 10px;
  padding: 10px;
}
.red_color {
  color: red;
}
</style>
