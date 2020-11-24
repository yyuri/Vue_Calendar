<template>
<v-app>
  <Calendar :locale="'es'">
    <template v-slot:toolbar="{today, forward, back, title, daily, weekly, monthly, type}">
      <v-toolbar dense flat style="padding: 0; margin: 0;">
        <div class="text-center paddless" style="width: 14.3vh !important;">
          <v-btn text class="ma-0 pa-0" outlined color="blue" @click="today">Avui</v-btn>
        </div>
        <v-divider vertical class="mx-2"></v-divider>
        <v-btn small icon @click="back">
          <v-icon color="blue lighten-1"> mdi-arrow-left-circle </v-icon>
        </v-btn>
        <v-btn small icon @click="forward">
          <v-icon color="blue lighten-1"> mdi-arrow-right-circle </v-icon>
        </v-btn>

        <v-toolbar-title class="overline text-weight-bold ml-2">
        {{title}}
        </v-toolbar-title>

        <template v-if="$vuetify.breakpoint.mdAndUp">
          <v-spacer></v-spacer>
          <v-spacer></v-spacer>
          <v-btn-toggle
            :value="type"
            tile
            color="blue accent-2"
            group
          >
            <v-btn :value="1" @click="daily">
              Diaris
            </v-btn>

            <v-btn :value="7" @click="weekly">
              Setmanal
            </v-btn>

            <v-btn :value="31" @click="monthly">
              Mensual
            </v-btn>
          </v-btn-toggle>
        </template>
      </v-toolbar>
    </template>
  </Calendar>
</v-app>
</template>

<script>
import Calendar from './components/Calendar.vue'

export default {
  name: 'App',
  components: {
    Calendar
  }
}
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
</style>
