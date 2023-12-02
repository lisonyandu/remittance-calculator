<template>
  <div class="remittance-app">
    <h1>Remittance Calculator</h1>

    <form @submit.prevent="calculateFees">
      <div class="form-group">
        <label for="sendingCountry" class="form-label">Sending Country:</label>
        <select v-model="sendingCountry" id="sendingCountry" required class="form-control">
          <option value="USD">United States (USD)</option>
          <option value="EUR">European Union (EUR)</option>
          <option value="GBP">United Kingdom (GBP)</option>
          <option value="CAD">Canada (CAD)</option>
          <option value="AUD">Australia (AUD)</option>
        </select>
      </div>

      <div class="form-group">
        <label for="sendingAmount" class="form-label">Amount to Send:</label>
        <input type="number" v-model.number="sendingAmount" min="1" id="sendingAmount" required class="form-control">
      </div>

      <div class="form-group">
        <label for="receivingCountry" class="form-label">Receiving Country:</label>
        <select v-model="receivingCountry" id="receivingCountry" required class="form-control">
          <option value="ZAR">South Africa (ZAR)</option>
          <option value="INR">India (INR)</option>
          <option value="PHP">Philippines (PHP)</option>
          <option value="MXN">Mexico (MXN)</option>
          <option value="BRL">Brazil (BRL)</option>
          <option value="NGN">Nigeria (NGN)</option>
        </select>
      </div>

      <div class="form-group">
        <label for="cryptoCurrency" class="form-label">CryptoCurrency:</label>
        <select v-model="cryptoCurrency" id="cryptoCurrency" required class="form-control">
          <option value="CELO">CELO</option>
          <option value="Ripple">Ripple</option>
          <option value="Bitcoin">Bitcoin</option>
          <option value="Ether">Ether</option>
          <option value="Algorand">Algorand</option>
        </select>
      </div>

      <button type="submit" class="btn btn-primary">Calculate Fees</button>
    </form>

    
    <div v-if="providers.length">
    
      <div class="fee-comparison">
      <div class="crypto-fee">
    <h3>Cryptocurrency Method</h3>
    <div class="crypto-card">
      <p >Amount Received: {{ 'ZAR' + ' ' + cryptoAmount }}</p>
    </div>
  </div>

    </div>

    <div class="results" v-if="cryptoFee && traditionalFee">
      <p v-if="cryptoFee < traditionalFee">
        Saving {{ formattedSavings }} by using cryptocurrency!
      </p>

      <p v-else>Fees are equal for both methods.</p>
    </div>


    <div class="traditional-fee">
        <h3>Traditional Method</h3>
     
      </div>
    <table class="styled-table">
      <thead>
        <tr>
          <th>Provider</th>
          <th>Amount Received</th>
          <th>Rate</th>
          <th>Fee</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="provider in providers" :key="provider.Provider">
          <td>{{ provider.Provider }}</td>
          <td>{{ 'ZAR' + ' ' + provider['Received Amount'] }}</td>
          <td>{{ provider.Rate }}</td>
          <td>{{ provider.Fee }}</td>
        </tr>
      </tbody>
    </table>
  </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      sendingAmount: 0,
      sendingCountry: 'USD',
      cryptoCurrency: 'CELO',
      receivingCountry: 'ZAR',
      cryptoFee: null,
      traditionalFee: null,
      formattedCryptoFee: null,
      formattedTraditionalFee: null,
      formattedSavings: null,
      cryptoAmount: null,
      providers: []
    };
  },
  methods: {
    async calculateFees() {
  try {
    const response = await axios.post('https://celo-labs-hackathon-backend.vercel.app/convert', {
      amount: this.sendingAmount,
      senderCurrencyCode: this.sendingCountry,
      cryptoCurrencyCode: this.cryptoCurrency,
      receiverCurrencyCode: this.receivingCountry,
    });

    this.cryptoAmount = response.data.cryptoAmount;
    this.providers = response.data.providers;

    const cryptoFeeData = this.providers.find((provider) =>
      provider.cryptoCurrencyCode === this.cryptoCurrency
    );
    if (cryptoFeeData) {
      this.cryptoFee = cryptoFeeData.fee;
      this.formattedCryptoFee = this.cryptoFee.toFixed(2);
    } else {
      this.cryptoFee = null;
      this.formattedCryptoFee = null;
    }

    const traditionalFeeData = this.providers.find((provider) =>
      provider.cryptoCurrencyCode === 'traditional'
    );
    if (traditionalFeeData) {
      this.traditionalFee = traditionalFeeData.fee;
      this.formattedTraditionalFee = this.traditionalFee.toFixed(2);
    } else {
      this.traditionalFee = null;
      this.formattedTraditionalFee = null;
    }

    if (this.cryptoFee && this.traditionalFee) {
      this.formattedSavings = Math.abs(this.cryptoFee - this.traditionalFee).toFixed(2);
    } else {
      this.formattedSavings = null;
    }
  } catch (error) {
    console.error(error);
  }
}}}
</script>

<style scoped>
.remittance-app {
  width: 500px;
  margin: 0 auto;
  padding: 20px;
  border-radius: 10px;
  background-color: #fff;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.2);
}

.form-control {
  width: 100%;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #fff;
  margin-bottom: 10px; /* Add margin-bottom to create space */
}

.btn-primary {
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
}

.fee-comparison,
.results {
  margin-top: 20px;
}

.styled-table {
  border-collapse: collapse;
  width: 100%;
  border: 1px solid #ddd;
}

.styled-table th,
.styled-table td {
  padding: 8px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

.styled-table th {
  background-color: #f0f0f0;
  border-top: 1px solid #ddd;
  font-weight: bold;
}
.crypto-fee {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 20px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.crypto-card {
  width: 400px;
  padding: 20px;
  background-color: #f2f2f2;
  border: 1px solid #ccc;
  border-radius: 5px;
  text-align: center;
}
</style>

