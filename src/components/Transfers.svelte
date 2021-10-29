<script lang="ts">
  export let Tezos, userAddress, setUserBalance;

  let loading = false;
  let recipient = "";
  let amount = "";

  const sendTransfer = async (): Promise<void> => {
    if (recipient && amount) {
      loading = true;
      try {
        const op = await Tezos.wallet
          .transfer({ to: recipient, amount: parseFloat(amount) })
          .send();
        await op.confirmation();
        recipient = "";
        amount = "";
        const balance = await Tezos.tz.getBalance(userAddress);
        setUserBalance(balance.toNumber());
      } catch (error) {
        console.log(error);
      } finally {
        loading = false;
      }
    }
  };
</script>

<h3 class="text-align-center">Make a transfer</h3>
<div id="transfer-inputs">
  <input
    type="text"
    id="input-recipient"
    placeholder="Recipient"
    value={recipient}
    on:input={e => (recipient = e.target.value)}
  />
  <input
    type="text"
    id="input-amount"
    placeholder="Amount"
    value={amount}
    on:input={e => (amount = e.target.value)}
  />
  <button
    class="button"
    disabled={!recipient && !amount}
    on:click={sendTransfer}
  >
    {#if loading}
      <span>
        <i class="fas fa-spinner fa-spin" />&nbsp; Please wait
      </span>
    {:else}
      <span>
        <i class="far fa-paper-plane" />&nbsp; Send
      </span>
    {/if}
  </button>
</div>
