<template>
    <main>
        <h1>The current value of Mike's extra $100 per paycheck is</h1>
        <Loading v-if="loading"/>
        <h2 v-else>${{ currentValue }}</h2>
        <div class="select-wrap">
            <span>Investment:</span>
            <select v-model="symbol" @change="calculate">
                <option value="SPY">S&amp;P 500 ETF</option>
                <option value="AMD">AMD</option>
                <option value="AAPL">Apple</option>
                <option value="BYND">Beyond Meat</option>
                <option value="DIS">Disney</option>
                <option value="FB">Facebook</option>
                <option value="GE">GE</option>
                <option value="MSFT">Microsoft</option>
                <option value="SBUX">Starbucks</option>
                <option value="TSLA">Tesla</option>
                <option value="UBER">Uber</option>
            </select>
        </div>
    </main>
</template>

<script>
import _ from 'lodash';
import axios from 'axios';
import Loading from './Loading';

export default {
  name: 'HelloWorld',
  components: {
    Loading,
  },
  data() {
    return {
      currentPrice: null,
      loading: false,
      symbol: 'SPY',
      apiKey: 'bpc69o7rh5rfp0uqdgsg',
      dates: [
        '1/15/2020',
        '2/01/2020',
        '2/15/2020',
        '3/01/2020',
        '3/15/2020',
        '4/01/2020',
        '4/15/2020',
        '5/01/2020',
        '5/15/2020',
        '6/01/2020',
        '6/15/2020',
        '7/01/2020',
        '7/15/2020',
        '8/01/2020',
        '8/15/2020',
        '9/01/2020',
        '9/15/2020',
      ],
      pastPrices: [],
    };
  },
  created() {
    this.calculate();
  },
  methods: {
    calculate() {
      this.loading = true;
      this.pastPrices = [];
      this.getCurrentPrice(this.symbol);
      this.getPastPrices(this.symbol);
    },
    getCurrentPrice(symbol) {
      const url = `https://finnhub.io/api/v1/quote/?symbol=${symbol}&token=${this.apiKey}`;

      axios.get(url)
        .then((response) => {
          this.currentPrice = response.data.c;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    getPastPrices(symbol) {
      const promises = [];
      const today = new Date().getTime();

      _.forEach( this.dates, (date) => {
        const payday = new Date(date).getTime();
        if ( today >= payday ) {
          promises.push(this.getPrice(symbol, date));
        }
      });

      Promise.all(promises).then( () => {
        this.loading = false;
      });
    },
    getPrice(symbol, date) {
      const day = new Date(date);
      let timeStart = Math.round(day.getTime()/1000);

      if ( day.getDay() === 6 ) {
        timeStart += 172800;
      }

      if ( day.getDay() === 0 ) {
        timeStart += 86400;
      }

      const timeEnd = timeStart + 86400;
      const url = `https://finnhub.io/api/v1/stock/candle?symbol=${symbol}&resolution=60&from=${timeStart}&to=${timeEnd}&token=${this.apiKey}`;
      return axios.get(url)
        .then( (response) => {
          if ( response.data.c ) {
            console.log('past price: ', response.data.c[0]);
            this.pastPrices.push( response.data.c[0] );
          } else {
            console.log('!!!! no price data for this day', date);
          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },
  computed: {
    currentValue() {
      const lots = [];

      if ( !this.pastPrices.length ) {
        return 0;
      }

      _.forEach( this.pastPrices, (price) => {
        const percentGain = ( this.currentPrice - price ) / price;
        lots.push( 100 + (100 * percentGain) );
      });

      const total = lots.reduce((a, b) => a + b, 0);
      return Math.round(total * 100) / 100
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

h1 {
    font-size: 28px;
    margin: 0;
    line-height: 1.4;
}

h2 {
  font-size: 80px;
  line-height: 1.2;
  color: #28881c;
  margin: 40px 0 0;
}

@media screen and ( max-width: 768px ) {
    h1 {
        font-size: 21px;
    }
    h2 {
        font-size: 60px;
        margin-top: 20px;
    }
}

main {
    padding-left: 1rem;
    padding-right: 1rem;
    height: 100vh;
    max-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding-top: 20vh;
    padding-bottom: 32px;
}

.select-wrap {
    margin-top: auto;
}
.select-wrap span {
    margin-right: 10px;
}
</style>
