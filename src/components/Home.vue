<template>
  <v-ons-page>
    <v-ons-toolbar>
      <div class="center">Coinmarket</div>
    </v-ons-toolbar>

    

    <v-ons-list>
      <v-ons-progress-bar indeterminate v-if="is_loading"></v-ons-progress-bar>
      <!-- Start for loop to show all coins from API -->
      <v-ons-list-item v-for="coin in coins" :key="coin.id">
        <div class="left">
          <!-- Get image placeholder -->
          <img class="list-item__thumbnail" :src="`https://dummyimage.com/40/000000/ffffff?text=${coin.symbol}`">
        </div>
        <div class="center">
          <span class="list-item__title">
            <!-- Show name of the coin -->
            {{coin.name}}
            <span
             :class="{'_red':coin.quotes.USD.percent_change_1h<0,'_green':coin.quotes.USD.percent_change_1h>=0}"
            >
            <!-- Show percent change for the last 1 hour -->
              {{
                coin.quotes.USD.percent_change_1h<0
                  ?coin.quotes.USD.percent_change_1h.toString().slice(1)
                  :coin.quotes.USD.percent_change_1h
              }}
            </span>
          </span>
          <!-- Show current price of the coin -->
          <span class="list-item__subtitle">{{coin.quotes.USD.price}}$</span>
        </div>
      </v-ons-list-item>
    </v-ons-list>
  </v-ons-page>
</template>

<script>
/**
 * Set-up API url
 */
const API_URL = 'https://api.coinmarketcap.com/v2';
/**
 * Set-up API endpoints
 */
const API_ENDPOINTS = {
  listings:'/listings/',
  ticker:'/ticker/'
};

import axios from 'axios';

export default {
  name: 'Home',

  data () {
    return {
      /**
       * Variable to save all coins from API
       */
      coins: [],

      /**
       * Variable to show progress bar 
       */
      is_loading: true,

    }
  },

  methods:{
    /**
     * Main method to fetch data from API
     */
    getCoins(){
      axios.get(API_URL+API_ENDPOINTS.ticker)
        .then( res => {
          /**
           * Save result to variable
           */
          this.coins = res.data.data;

          /**
           * Change loading status
           */
          if(this.is_loading)
            this.is_loading = false;

        })
        .catch( err => {

          console.error(err);
          throw new Error(err);
        })
    }

  },

  mounted(){
    /**
     * Set timer before fetching data
     */
    setTimeout(()=>{
        this.getCoins();
      },1500);
    
  }
}
</script>

<style>
span._green {
  font-weight: bold;
  color:green !important;
}
span._green::before {
  content:'+';
  font-weight: bold;
  color:green !important;
}
span._red::before {
  content:'-';
  font-weight: bold;
  color:red !important;
}
span._red {
  font-weight: bold;
  color:red !important;
}
</style>
