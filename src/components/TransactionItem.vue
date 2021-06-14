<template>
  <!-- NOTE Could use v-for over data object instead -->
  <li>
    <label>Tx:</label>
    {{ signature }}
  </li>
  <li>
    <label>Fee:</label>
    {{ fee }}
  </li>
  <li>
    <label>Sent Amount:</label>
    {{ sentAmount }}
  </li>
  <li>
    <label>Sender:</label>
    {{ senderAddress }}
  </li>
  <li>
    <label>Sender Balance:</label>
    {{ senderBalance }}
  </li>
  <li>
    <label>Receiver:</label>
    {{ receiverAddress }}
  </li>
  <li>
    <label>Receiver Balance:</label>
    {{ receiverBalance }}
  </li>
</template>

<script lang="ts">
import { defineComponent, PropType, computed } from "vue";
import { TransactionWithSignature } from "../composables/getTransactions";

export default defineComponent({
  name: "TransactionItem",
  props: {
    transaction: {
      type: Object as PropType<TransactionWithSignature>,
      require: true,
    },
  },
  // TS7006 ERROR The above won't compile as props implicitly has an 'any' type
  // Going to try making an interface instead
  // NOTE May need to add type to ctx: SetupContext<Record<string, any>>...ERROR!
  // UPDATE Errors occurred because I didn't use defineComponent() for TS!
  setup(props, ctx) {
    // Let's retrieve the data we need from the transaction
    const signature = computed(() => {
      return props.transaction?.signature;
    });
    const fee = computed(() => {
      return props.transaction?.confirmedTransaction.meta?.fee;
    });
    const sentAmount = computed(() => {
      // Q: Must I check whether meta exists?
      // A: Yes, otherwise I get error saying balance could be undefined
      if (props.transaction?.confirmedTransaction.meta) {
        const senderPreBalance =
          props.transaction?.confirmedTransaction.meta?.preBalances[0];
        const senderPostBalance =
          props.transaction?.confirmedTransaction.meta?.postBalances[0];
        return senderPreBalance - senderPostBalance;
      } else {
        console.log("Nothing sent! Returning 0 for amount");
        return 0;
      }
    });
    const senderAddress = computed(() => {
      return props.transaction?.confirmedTransaction.transaction.instructions[0].keys[0].pubkey.toBase58();
    });
    const senderBalance = computed(() => {
      return props.transaction?.confirmedTransaction.meta?.postBalances[0];
    });
    const receiverAddress = computed(() => {
      return props.transaction?.confirmedTransaction.transaction.instructions[0].keys[1].pubkey.toBase58();
    });
    const receiverBalance = computed(() => {
      return props.transaction?.confirmedTransaction.meta?.postBalances[1];
    });

    // NOTE I could place all this into a consolidated data object
    // and then use v-for for template. For now, keeping simple/explicit
    return {
      signature,
      fee,
      sentAmount,
      senderAddress,
      senderBalance,
      receiverAddress,
      receiverBalance,
    };
  },
});
</script>

<style>
li {
  list-style-type: none;
  text-align: left;
  padding: 0;
  margin: 0;
}

label {
  font-weight: bold;
}
</style>
