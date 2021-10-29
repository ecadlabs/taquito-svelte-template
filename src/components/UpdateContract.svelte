<script lang="ts">
  import { onMount } from "svelte";

  export let contractAddress, userAddress, setUserBalance, Tezos;

  let loadingIncrement = false;
  let loadingDecrement = false;
  let contract;
  let storage: number | null = null;

  const increment = async (): Promise<void> => {
    loadingIncrement = true;
    try {
      const op = await contract.methods.increment(1).send();
      await op.confirmation();
      const newStorage: any = await contract.storage();
      if (newStorage) {
        storage = newStorage.toNumber();
      }
      setUserBalance(await Tezos.tz.getBalance(userAddress));
    } catch (error) {
      console.log(error);
    } finally {
      loadingIncrement = false;
    }
  };

  const decrement = async (): Promise<void> => {
    loadingDecrement = true;
    try {
      const op = await contract.methods.decrement(1).send();
      await op.confirmation();
      const newStorage: any = await contract.storage();
      if (newStorage) {
        storage = newStorage.toNumber();
      }
      setUserBalance(await Tezos.tz.getBalance(userAddress));
    } catch (error) {
      console.log(error);
    } finally {
      loadingDecrement = false;
    }
  };

  onMount(async () => {
    contract = await Tezos.wallet.at(contractAddress);
    storage = await contract.storage();
  });
</script>

{#if !userAddress}
  <div>&nbsp;</div>
{:else}
  <div id="increment-decrement">
    <h3 class="text-align-center">
      Current counter: <span>{storage ?? "Loading..."}</span>
    </h3>
    <div class="buttons">
      <button class="button" disabled={loadingIncrement} on:click={increment}>
        {#if loadingIncrement}
          <span>
            <i class="fas fa-spinner fa-spin" />&nbsp; Please wait
          </span>
        {:else}
          <span>
            <i class="fas fa-plus" />&nbsp; Increment by 1
          </span>
        {/if}
      </button>
      <button class="button" on:click={decrement}>
        {#if loadingDecrement}
          <span>
            <i class="fas fa-spinner fa-spin" />&nbsp; Please wait
          </span>
        {:else}
          <span>
            <i class="fas fa-minus" />&nbsp; Decrement by 1
          </span>
        {/if}
      </button>
    </div>
  </div>
{/if}
