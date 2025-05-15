<template>
    <div class="container">
      <div class="calculator">
        <h1>Калькулятор тарифов</h1>
        <p>Этот калькулятор поможет вам определить, какой тариф более выгоден для вас в зависимости от цены товара и количества сделок.</p>
        <div>
          <label for="price-input">Цена товара (в рублях):</label>
          <input type="number" v-model="price" id="price-input" placeholder="Введите цену товара" />
        </div>
        <div>
          <label for="transactions-input">Количество сделок в месяц:</label>
          <input type="number" v-model="transactions" id="transactions-input" placeholder="Введите количество сделок в месяц" />
        </div>
        <div>
          <label>
            <input type="radio" v-model="isGross" :value="true" @change="calculate" /> Рассчитывать брутто
          </label>
          <label>
            <input type="radio" v-model="isGross" :value="false" @change="calculate" /> Рассчитывать нетто
          </label>
        </div>
        <div>
          <label for="free-commission-input">Комиссия Free:</label>
          <input type="number" v-model="freeCommission" id="free-commission-input" step="0.01" min="0" max="1" />
        </div>
        <div>
          <label for="standart-commission-input">Комиссия Standart:</label>
          <input type="number" v-model="standartCommission" id="standart-commission-input" step="0.01" min="0" max="1" />
        </div>
        <div>
          <button @click="calculate">Рассчитать</button>
        </div>
        <div v-if="result" class="results">
          <h2>Результаты:</h2>
          <div class="result-item">
            <h3>Тариф Free</h3>
            <p>{{ result.free.toFixed(2) }} рублей</p>
          </div>
          <div class="result-item">
            <h3>Тариф Standart (месячная оплата)</h3>
            <p>{{ (result.standartMonthly).toFixed(2) }} рублей в месяц</p>
          </div>
          <div class="result-item">
            <h3>Тариф Standart (полугодовая оплата)</h3>
            <p>{{ (result.standartSemiAnnual).toFixed(2) }} рублей в месяц</p>
          </div>
          <div class="result-item">
            <h3>Тариф Standart (годовая оплата)</h3>
            <p>{{ (result.standartAnnual).toFixed(2) }} рублей в месяц</p>
          </div>
        </div>
      </div>
    </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from 'vue';

export default defineComponent({
  name: 'Calculator',
  setup() {
    const price = ref(0);
    const transactions = ref(0);
    const isGross = ref(true);
    const result = ref<{ free: number; standartMonthly: number; standartSemiAnnual: number; standartAnnual: number } | null>(null);
    const freeCommission = ref(0.2);
    const standartCommission = ref(0.1);

    const calculateFree = () => {
      let freeCost;
      if (isGross.value) {
        const grossPrice = price.value / (1 - freeCommission.value);
        freeCost = transactions.value * Math.max(grossPrice - price.value, 150);
      } else {
        freeCost = transactions.value * Math.max(price.value * freeCommission.value, 150);
      }
      result.value = {
        ...result.value,
        free: freeCost,
      };
    };

    const calculateStandart = () => {
      let standartMonthly, standartSemiAnnual, standartAnnual;
      if (isGross.value) {
        const grossPrice = price.value / (1 - freeCommission.value);
        standartMonthly = transactions.value * Math.max(grossPrice * standartCommission.value, 70) + 6990;
        standartSemiAnnual = transactions.value * Math.max(grossPrice * standartCommission.value, 70) + 5990;
        standartAnnual = transactions.value * Math.max(grossPrice * standartCommission.value, 70) + 4990;
      } else {
        standartMonthly = transactions.value * Math.max(price.value * standartCommission.value, 70) + 6990;
        standartSemiAnnual = transactions.value * Math.max(price.value * standartCommission.value, 70) + 5990;
        standartAnnual = transactions.value * Math.max(price.value * standartCommission.value, 70) + 4990;
      }
      result.value = {
        ...result.value,
        standartMonthly,
        standartSemiAnnual,
        standartAnnual,
      };
    };

    const calculate = () => {
      calculateFree();
      calculateStandart();
    };

    freeCommission.value = parseFloat(freeCommission.value.toFixed(2));
    standartCommission.value = parseFloat(standartCommission.value.toFixed(2));

    watch([freeCommission, price, transactions, isGross], calculateFree);
    watch([standartCommission, price, transactions, isGross], calculateStandart);

    return {
      price,
      transactions,
      isGross,
      result,
      calculate,
      freeCommission,
      standartCommission,
    };
  },
});
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
}

.calculator {
  margin: 0 auto;
  width: 100%;
  max-width: 800px;
  padding: 40px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  background-color: #1e3a8a;
  color: #ffffff;
}

h1 {
  font-size: 2rem;
  margin-bottom: 1rem;
}

p {
  margin-bottom: 1.5rem;
  font-size: 1.125rem;
}

label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: bold;
}

input[type="number"] {
  width: 100%;
  padding: 0.75rem;
  margin-bottom: 1.5rem;
  border: 1px solid #3b82f6;
  border-radius: 4px;
  background-color: #1e40af;
  color: #ffffff;
}

button {
  width: 100%;
  padding: 1rem;
  background-color: #3b82f6;
  color: #ffffff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1.125rem;
}

button:hover {
  background-color: #2563eb;
}

.results {
  margin-top: 2rem;
  padding: 20px;
  background-color: #f3f4f6;
  border-radius: 8px;
  color: #1e3a8a;
}

.result-item {
  margin-bottom: 1rem;
}

.result-item h3 {
  margin-bottom: 0.5rem;
  font-size: 1.25rem;
}
</style> 