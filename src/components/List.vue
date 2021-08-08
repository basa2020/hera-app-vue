<template>
  <div class="hello">
    <h1>Zanr koje se najvise ponavlja : {{ zanrKojeSeNajvisePonavlja }}</h1>
    <SingleList
      v-for="rad of radovi"
      :key="rad.id"
      :rad="rad"
      :odabraniRadovi="odabraniRadovi"
      :znanstveniRadovi="znanstveniRadovi"
      :zanrKojeSeNajvisePonavlja="zanrKojeSeNajvisePonavlja"
    />
  </div>
</template>

<script>
import SingleList from "./SingleList.vue";
import { elementKojiSeNajvisePonavlja } from "../utils";
export default {
  components: { SingleList },
  name: "List",
  props: ["radovi", "odabraniRadovi", "znanstveniRadovi"],
  data() {
    return {
      zanrKojeSeNajvisePonavlja: null,
      znarKojiSePonavlja() {
         let zanrovi = [];
        this.radovi.forEach((rad) => {
          rad.zanr.forEach((element) => {
            zanrovi.push(element);
          });
        });
        this.zanrKojeSeNajvisePonavlja = elementKojiSeNajvisePonavlja(
          zanrovi
        ).newMostFrequent

      }
    }
  },
  mounted() {
       this.$watch("radovi", () => {
      this.znarKojiSePonavlja();
    });
  },

};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
