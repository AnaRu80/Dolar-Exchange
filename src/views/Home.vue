<template>
  <div class="home">
    <h1>Cambio de Dolar a Peso chileno</h1>
    <v-row>
      <v-col cols="12">
        <v-card>
          <v-date-picker
            full-width
            locale="es-419"
            v-model="fecha"
            :min="minimo"
            :max="maximo"
            @change="getDolar(fecha)"
          ></v-date-picker>
        </v-card>
        <v-card color="error" dark>
          <v-card-text class="display-1 text-center">
            {{ valor }} - Peso Chileno<br />
            {{ fecha }}
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";
import { mapMutations } from "vuex";

export default {
  name: "Home",

  data() {
    return {
      fecha: new Date().toISOString().substr(0, 10),
      minimo: "1984",
      maximo: new Date().toISOString().substr(0, 10),
      valor: null,
    };
  },
  created() {
    this.getDolar(this.fecha);
  },

  methods: {
    ...mapMutations(["mostrarLoading", "ocultarLoading"]),
    async getDolar(dia) {
      let arrayFecha = dia.split(["-"]);
      let ddmmyy = arrayFecha[2] + "-" + arrayFecha[1] + "-" + arrayFecha[0];
      try {
        this.mostrarLoading({
          titulo: "Accediendo a información",
          color: "secondary",
        });
        let datos = await axios.get(
          `https://mindicador.cl/api/dolar/${ddmmyy}`
        );
        if (datos.data.serie.length > 0) {
          this.valor = datos.data.serie[0].valor;
        } else {
          this.valor = "sin resultados";
        }
      } catch (error) {
        console.log(error);
      } finally {
        this.ocultarLoading();
      }
    },
  },
};
</script>
