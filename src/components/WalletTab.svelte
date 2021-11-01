<script lang="ts">
  export let userAddress, wallet, setWallet, setUserAddress, setUserBalance;

  const disconnectWallet = async () => {
    await wallet.client.destroy();
    setWallet(undefined);
    setUserAddress(undefined);
    setUserBalance(undefined);
  };
</script>

{#if userAddress}
  <div class="wallet-tab" on:click={disconnectWallet}>
    <i class="fa fa-power-off" />
    &nbsp;&nbsp; {userAddress.slice(0, 5)}...{userAddress.slice(-5)}
  </div>
{/if}
