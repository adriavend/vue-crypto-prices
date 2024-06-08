<template>
  <div class="container mt-2 pt-2">
    <div class="row">
        <div class="col-xl-4 col-md-4 col-sm-12 mb-4">
          <iframe style="width:320px;height:260px;border-radius:10px;box-shadow:2px 4px 4px rgb(0 0 0 / 25%);display:flex;justify-content:center;border:1px solid #bcbcbc" src="https://dolarhoy.com/i/cotizaciones/dolar-blue" frameborder="0">
          </iframe>
        </div>
        <div class="col-xl-4 col-md-4 col-sm-12 mb-4">
          <iframe style="width:320px;height:260px;border-radius:10px;box-shadow:2px 4px 4px rgb(0 0 0 / 25%);display:flex;justify-content:center;border:1px solid #bcbcbc" src="https://dolarhoy.com/i/cotizaciones/dolar-bancos-y-casas-de-cambio" frameborder="0">
          </iframe>
        </div>
        <div class="col-xl-4 col-md-4 col-sm-12 mb-4">
          <iframe style="width:320px;height:260px;border-radius:10px;box-shadow:2px 4px 4px rgb(0 0 0 / 25%);display:flex;justify-content:center;border:1px solid #bcbcbc" src="https://dolarhoy.com/i/cotizaciones/dolar-mep" frameborder="0">
          </iframe>
        </div>
    </div>
    <div class="row">
      <h1>ARS Exchanges</h1>
      <div class="col-xl-3 col-md-6 mb-4" v-for="exchange in exchanges" :key="exchange.name">
        <div class="card border-left-primary shadow h-100 py-2 bg-dark">
          <div class="card-body">
            <div class="row no-gutters align-items-center">
              <div class="col mr-2">
                <div class="text-xs font-weight-bold text-primary text-uppercase mb-1">
                  {{ exchange.name }}
                </div>
                <div class="h5 mb-0 font-weight-bold text-gray-800"> 
                  $ {{ exchange.totalAsk }} <sub class="text-muted">Ask</sub> / $ {{ exchange.totalBid }} <sub class="text-muted">Bid</sub> 
                </div>
              </div>
              <div class="col-auto">
                <i class="bi bi-cash-coin text-white card-exchange-icon"></i>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="row">
      <h1>Coin Market</h1>
      <input type="text" class="form-control bg-dark text-light rounded-0 border-o my-4" placeholder="Search Coin"
        @keyup="searchCoin()" v-model="textSearch" />
      <div class="table-responsive">
        <table class="table table-dark">
          <thead>
            <tr>
              <th v-for="title in coinTitles" :key="title">
                {{ title }}
              </th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(coin, index) in filteredCoins" :key="coin.id">
              <td class="text-muted">
                {{ index + 1 }}
              </td>
              <td>
                <img :src="coin.image" alt="icon" style="width: 2rem" class="me-2">
                <span>{{ coin.name }}</span>
                <span class="ms-2 text-uppercase text-muted">
                  {{ coin.symbol }}
                </span>
              </td>
              <td>
                $ {{ coin.current_price }}
              </td>
              <td :class="[coin.price_change_percentage_24h > 0 ? 'text-success' : 'text-danger']">
                {{ coin.price_change_percentage_24h }} %
              </td>
              <td>
                $ {{ coin.total_volume.toLocaleString() }}
              </td>
            </tr>
          </tbody>
      </table>
      </div>
      
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      coins: [],
      filteredCoins: [],
      coinTitles: [
        '#',
        'Coin',
        'Price',
        'Change',
        '24h Volume'
      ],
      textSearch: '',
      exchanges: [],
    }
  },
  async mounted() {
    await this.getCoins();
    await this.getExchanges();
  },
  methods: {
    async getCoins() {
      const res = await fetch('https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false');
      const data = await res.json();
      // console.log(data);
      this.coins = data;
      this.filteredCoins = data;
    },
    searchCoin() {
      this.filteredCoins = this.coins.filter(coin => coin.name.toLowerCase().includes(this.textSearch.toLowerCase())
        || coin.symbol.toLowerCase().includes(this.textSearch.toLowerCase()));
    },
    async getExchanges() {
      const res = await fetch('https://criptoya.com/api/USDT/ars/1');
      const data = await res.json();

      let aux = [];
      for (const [key, value] of Object.entries(data)){
          aux.push({ name: key, ...value});
      }
      this.exchanges = aux.sort(function(a, b) {
        return b.totalBid - a.totalBid;
      });
    }
  }
}
</script>

<style>
  .card-exchange-icon {
    font-size: 2rem;
  }
</style>
