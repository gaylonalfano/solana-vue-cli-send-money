<template>
  <h2>Send Money to Solana</h2>
  <form class="send-container">
    <div class="send-inputs">
      <div>
        <label for="amount">Amount (lamports)</label>
        <input
          v-model="amount"
          name="amount"
          type="number"
          required
          placeholder="0"
        />
      </div>
      <div>
        <label for="address">Address</label>
        <input v-model="address" name="address" type="text" required />
      </div>
    </div>
    <div>
      <button type="submit" @click.prevent="handleSendMoney">Submit</button>
    </div>
  </form>
</template>

<script lang="ts">
import { ref, defineComponent } from "vue";
import { sendMoney } from "../composables/useWallet";
export default defineComponent({
  name: "SenderForm",
  emits: ["onSendMoney"],
  setup(props, ctx) {
    const amount = ref<number>(0);
    const address = ref<string>("");

    async function handleSendMoney(e: MouseEvent) {
      console.log("handleSendMoney triggered", e);
      await sendMoney(address.value, amount.value);
      // Emit onSendMoney event to parent App so it
      // can execute
      ctx.emit("onSendMoney");
    }
    return { amount, address, handleSendMoney };
  },
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

form {
  margin: 0 auto;
  padding: 10px 0;
  width: 600px;
  border: 1px solid black;
}

.send-inputs {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.send-inputs div {
  width: 50%;
  text-align: right;
  margin: 5px 0;
}
</style>
