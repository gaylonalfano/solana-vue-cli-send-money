<template>
  <!-- <div v-if="walletRef"> -->
  <!--   <h1>Wallet: {{ walletRef.publicKey }}</h1> -->
  <!-- </div> -->
  <SenderForm @onSendMoney="didSendMoney" />
  <div v-if="transactionsRef">
    <TransactionsList :transactions="transactionsRef" />
  </div>
</template>

<script lang="ts">
import { Connection } from "@solana/web3.js";
import { defineComponent, watchEffect, ref } from "vue";
import SenderForm from "./components/SenderForm.vue";
import TransactionsList from "./components/TransactionsList.vue";
import { initWallet, WalletAdapter } from "./composables/useWallet";
import {
  getTransactions,
  TransactionWithSignature,
} from "./composables/getTransactions";

export default defineComponent({
  name: "App",
  components: {
    SenderForm,
    TransactionsList,
  },
  setup: () => {
    const transactionsRef = ref<TransactionWithSignature[]>();
    const connectionRef = ref<Connection>();
    const walletRef = ref<WalletAdapter>();

    watchEffect(() => {
      console.log("watchEffect triggered");
      initWallet().then(([connection, wallet]: [Connection, WalletAdapter]) => {
        // Update our Refs so we can reuse inside didSendMoney()
        connectionRef.value = connection;
        walletRef.value = wallet;
        //console.log(connectionRef.value);
        //console.log(walletRef.value);

        // Make sure wallet has publicKey
        // Q: Should I check walletRef or just wallet?
        if (wallet.publicKey) {
          console.log("walletRef.value.publicKey: ", walletRef.value.publicKey); // Proxy
          console.log("wallet.publicKey: ", wallet.publicKey); // PublicKey
          // Let's fetch recent confirmed transactions for this wallet
          getTransactions(connection, wallet.publicKey).then(
            (transactions: TransactionWithSignature[]) => {
              console.log("transactions: ", transactions);
              // Update our transactionsRef
              transactionsRef.value = transactions;
            }
          );
        }
      });
    });

    function didSendMoney() {
      console.log("didSendMoney triggered");
      getTransactions(connectionRef.value!, walletRef.value!.publicKey!).then(
        (transactions: TransactionWithSignature[]) => {
          transactionsRef.value = transactions;
        }
      );
    }

    return { transactionsRef, walletRef, didSendMoney };
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
