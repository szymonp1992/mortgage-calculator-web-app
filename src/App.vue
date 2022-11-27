<template>
  <div class="app-container">
    <h2>Mortgage calculator</h2>
    <div class="calculator-container">
      <div>
        <label for="purchase-price">Purchase price: ${{ purchasePrice }}</label>
        <input
          type="range"
          id="purchase-price"
          name="purchase-price"
          min="0"
          max="1000000"
          step="10000"
          :value="purchasePrice"
          @input="changeValue"
        />
      </div>
      <div>
        <label for="down-payment">Down payment: ${{ downPayment }}</label>
        <input
          type="range"
          id="down-payment"
          name="down-payment"
          min="0"
          max="1000000"
          step="10000"
          :value="downPayment"
          @input="changeValue"
        />
      </div>
      <div>
        <label for="repayment-time"
          >Repayment time: {{ repaymentTime }} years</label
        >
        <input
          type="range"
          id="repayment-time"
          name="repayment-time"
          min="1"
          max="25"
          step="1"
          :value="repaymentTime"
          @input="changeValue"
        />
      </div>
      <div>
        <label for="interest-rate"
          >Interest rate: {{ (interestRate * 100).toFixed() }}%</label
        >
        <input
          type="range"
          id="interest-rate"
          name="interest-rate"
          min="0"
          max="0.2"
          step="0.01"
          :value="interestRate"
          @input="changeValue"
        />
      </div>
      <div>
        <p>Loan amount</p>
        <h2>${{ loanAmount }}</h2>
      </div>
      <div>
        <p>Estimated pr. month:</p>
        <h2>${{ perMonth }}</h2>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";

export default {
  setup() {
    const purchasePrice = ref(500000);
    const downPayment = ref(100000);
    const repaymentTime = ref(5);
    const interestRate = ref(0.03);
    const loanAmount = ref(0);
    const perMonth = ref(0);

    onMounted(() => {
      // loanAmount.value = purchasePrice.value - downPayment.value;
      calculateRate();
    });

    function calculateRate() {
      loanAmount.value = purchasePrice.value - downPayment.value;
      const totalLoanAmount = loanAmount.value;
      console.log("total loan amount: ", totalLoanAmount);
      const repaymentMonths = repaymentTime.value * 12;
      console.log("repayment months: ", repaymentMonths);
      const monthlyInterestRate = interestRate.value / 12;
      console.log("monthly interest rate: ", monthlyInterestRate);
      let monthlyPayment;
      if (!monthlyInterestRate) {
        monthlyPayment = totalLoanAmount / repaymentMonths;
        perMonth.value = monthlyPayment.toFixed(2);
        return;
      }
      monthlyPayment =
        (totalLoanAmount *
          (monthlyInterestRate *
            Math.pow(1 + monthlyInterestRate, repaymentMonths))) /
        (Math.pow(1 + monthlyInterestRate, repaymentMonths) - 1);
      perMonth.value = monthlyPayment.toFixed(2);
    }

    function changeValue(e) {
      if (e.target.id === "purchase-price") {
        purchasePrice.value = e.target.value;
      }
      if (e.target.id === "down-payment") {
        downPayment.value = e.target.value;
      }
      if (e.target.id === "repayment-time") {
        repaymentTime.value = e.target.value;
      }
      if (e.target.id === "interest-rate") {
        interestRate.value = e.target.value;
      }
      // loanAmount.value = purchasePrice.value - downPayment.value;
      // const p = loanAmount.value;
      // const i = interestRate.value / 12;
      // const n = repaymentTime.value * 12;
      // perMonth.value = (p * (((i * (1 + i)) ^ n) / ((1 + i) ^ (n - 1)))) / n;
      // perMonth.value.toFixed();
      calculateRate();
    }

    return {
      purchasePrice,
      downPayment,
      repaymentTime,
      interestRate,
      loanAmount,
      perMonth,
      changeValue,
    };
  },
};
</script>

<style>
* {
  background-color: #094b6e;
  color: white;
  font-family: Roboto, Arial, sans-serif;
}

.app-container {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  height: 60%;
  width: 80%;
  border: 10px solid white;
  border-radius: 15px;
  padding: 15px;
}

@media (max-width: 700px) {
  * {
    font-size: 0.8rem;
  }
  .app-container {
    height: 40%;
  }
}

.calculator-container {
  margin: 2rem auto;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-between;
}

.calculator-container div {
  width: 30%;
  display: flex;
  flex-direction: column;
  margin: 0.5rem 0;
}
.calculator-container div * {
  margin: 1rem 0;
}
</style>