<script lang="ts">
  export let wallet, setWallet, setUserAddress, setUserBalance;

  const disconnectWallet = async () => {
    await wallet.client.destroy();
    setWallet(undefined);
    setUserAddress(undefined);
    setUserBalance(undefined);
  };
</script>

<div class="buttons">
  <button class="button" on:click={disconnectWallet}>
    <i class="fas fa-times" />&nbsp; Disconnect wallet
  </button>
</div>
