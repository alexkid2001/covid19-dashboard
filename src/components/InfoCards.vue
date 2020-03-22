<template>
  <v-row class="info-cards-wrap">
    <v-col cols="12" sm="4"
    >
      <v-card
        class="mx-auto v-card_confirmed"
      >
        <v-card-title>
          Confirmed:
        </v-card-title>
        <v-card-text>
          {{ infoData.confirmed }}
        </v-card-text>
      </v-card>
    </v-col>
    <v-col cols="12" sm="4"
    >
      <v-card
        class="mx-auto v-card_recovered"
      >
        <v-card-title>
          Rrecovered:
        </v-card-title>
        <v-card-text>
          {{ infoData.recovered }}
        </v-card-text>
      </v-card>
    </v-col>
    <v-col cols="12" sm="4"
    >
      <v-card
        class="mx-auto v-card_deaths"
      >
        <v-card-title>
          Deaths:
        </v-card-title>
        <v-card-text>
          {{ infoData.deaths }}
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  props: ['infoData'],
};
</script>

<style>

</style>
