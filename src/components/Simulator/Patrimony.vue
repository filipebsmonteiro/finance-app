<template>
  <div class="">
    <p class="text-center">
      Seu saldo Ao fim do mês é:
      <b>{{ $formaters.money(incomes - expenses) }}</b>
    </p>
    <p class="text-caption text-center q-mb-none">
      Para alcançar a independência em
    </p>
    <q-input type="number" v-model="months" min="1" class="inline" stack-label>
      <template v-slot:prepend>
        <q-btn flat rounded icon="remove" @click="months > 1 ? months-- : null" />
      </template>
      <template v-slot:append>
        <q-btn flat rounded icon="add" @click="months++" />
      </template>
    </q-input>
    <p class="text-caption text-center q-mb-none">Meses</p>
    <ul class="text-caption">
      <li>
        <b>
          Seu <u>patrimônio</u> acumulado deve ser:
          <span class="text-secondary">
            {{ $formaters.money(independency.patrimony) }}
          </span>
        </b>
      </li>
      <li>
        <b>
          Seus <u>Rendimentos</u> serão:
          <span class="text-positive">
            {{ $formaters.money(independency.investmentIncome) }}
          </span>
        </b>
      </li>
      <li>
        <b>
          Seus <u>custos</u> corrigidos pela inflação:
          <span class="text-red">
            {{ $formaters.money(independency.expensesOnFuture) }}
          </span>
        </b>
      </li>
    </ul>
  </div>
</template>

<script>
import { mapState } from "pinia";
import { useProjectionStore } from "src/stores/projection";

export default {
  name: "PatrimonySimulator",
  computed: {
    ...mapState(useProjectionStore, [
      "patrimony",
      "incomes",
      "investment",
      "expenses",
      "inflation",
    ]),
    independency() {
      // TODO: Validar melhor com negativos oque pode impedir a independencia

      const expenseGrowthPercentage = this.inflation / 100;
      const expensesOnFuture = [...Array(parseInt(this.months))].reduce(
        (acc) => parseFloat((acc + acc * expenseGrowthPercentage).toFixed(2)),
        this.expenses
      );

      const investmentGrowthPercentage = this.investment / 100;
      const initialPatrimony = this.patrimony > 0 ? this.patrimony : 1;
      const initialInvestmentIncome = parseFloat(
        (initialPatrimony * investmentGrowthPercentage).toFixed(2)
      );
      let investmentIncome = initialInvestmentIncome;
      let patrimony = initialPatrimony;
      while (investmentIncome < expensesOnFuture) {
        investmentIncome = parseFloat(
          (patrimony * investmentGrowthPercentage).toFixed(2)
        );
        patrimony += parseFloat(
          (this.incomes - this.expenses + investmentIncome).toFixed(2)
        ); // Adicionar investimentIncome é juros compostos
      }

      return {
        expensesOnFuture,
        investmentIncome,
        patrimony,
      };
    },
  },
  data() {
    return {
      months: 24,
    };
  },
};
</script>
