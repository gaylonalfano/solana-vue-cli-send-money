<template>
  <div class="transactions-container">
    <ul
      class="transaction"
      v-for="transaction in transactions"
      :key="transaction.signature"
    >
      <TransactionItem :transaction="transaction" />
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";
import { TransactionWithSignature } from "../composables/getTransactions";
import TransactionItem from "../components/TransactionItem.vue";

export default defineComponent({
  name: "TransactionsList",
  components: {
    TransactionItem,
  },
  // Q: How to assign types to props?
  // NOTE Use PropType to assign type to props
  // https://stackoverflow.com/questions/64831745/props-typing-in-vue-js-3-with-typescript
  // https://forum.vuejs.org/t/specifying-array-type-of-prop-in-vue-3-0-with-typescript/103169
  props: {
    // NOTE Need to use required: true otherwise it can be undefined!
    //transactions: Array as PropType<Array<TransactionWithSignature>>,
    transactions: {
      type: Array as PropType<Array<TransactionWithSignature>>,
      required: true,
    },
  },
  // setup(props, ctx) {
  // Q: Do I need to loop over props to simplify template variables?
  // Or, can I just use :key="t.signature" and then get values?
  // Let's loop over the props transactions to get the data pieces we want to display
  // TODO Either loop over and simplify vars, or build directly inside template
  // TODO Could consider using scoped/named slots as well
  //function getTransactionItemDetails() {
  //  props.transactions.map((transaction) => {
  //    console.log(transaction);
  //  });
  //}
  // },
});
</script>

<style scoped>
a {
  color: #42b983;
}

label {
  margin: 0 0.5em;
  font-weight: bold;
}

code {
  background-color: #eee;
  padding: 2px 4px;
  border-radius: 4px;
  color: #304455;
}
</style>
