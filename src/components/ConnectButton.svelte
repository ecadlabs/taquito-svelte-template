<script lang="ts">
  import { onMount } from "svelte";
  import { BeaconWallet } from "@taquito/beacon-wallet";
  import {
    NetworkType,
    BeaconEvent,
    defaultEventCallbacks
  } from "@airgap/beacon-sdk";
  import TransportU2F from "@ledgerhq/hw-transport-u2f";
  import { LedgerSigner } from "@taquito/ledger-signer";

  export let setWallet, wallet, setUserAddress, Tezos, setUserBalance;

  let loadingNano = false;

  const connectWallet = async (): Promise<void> => {
    try {
      await wallet.requestPermissions({
        network: {
          type: NetworkType.HANGZHOUNET,
          rpcUrl: "https://hangzhounet.api.tez.ie"
        }
      });
      // gets user's address
      const userPkh = await wallet.getPKH();
      setUserAddress(userPkh);
      setWallet(wallet);
      const balance = await Tezos.tz.getBalance(userPkh);
      setUserBalance(balance.toNumber());
    } catch (error) {
      console.log(error);
    }
  };

  const connectNano = async (): Promise<void> => {
    try {
      loadingNano = true;
      const transport = await TransportU2F.create();
      const ledgerSigner = new LedgerSigner(transport, "44'/1729'/0'/0'", true);

      Tezos.setSignerProvider(ledgerSigner);

      //Get the public key and the public key hash from the Ledger
      const userPkh = await Tezos.signer.publicKeyHash();
      await setUserAddress(userPkh);
      const balance = await Tezos.tz.getBalance(userPkh);
      setUserBalance(balance.toNumber());
    } catch (error) {
      console.log("Error!", error);
      loadingNano = false;
    }
  };

  onMount(async () => {
    if (!wallet) {
      // creates a wallet instance
      const newWallet = new BeaconWallet({
        name: "Taquito Boilerplate",
        preferredNetwork: NetworkType.HANGZHOUNET,
        disableDefaultEvents: true, // Disable all events / UI. This also disables the pairing alert.
        eventHandlers: {
          // To keep the pairing alert, we have to add the following default event handlers back
          [BeaconEvent.PAIR_INIT]: {
            handler: defaultEventCallbacks.PAIR_INIT
          }
          /*[BeaconEvent.PAIR_SUCCESS]: {
            handler: data => setPublicToken(data.publicKey)
          }*/
        }
      });
      Tezos.setWalletProvider(newWallet);
      setWallet(newWallet);
      // checks if wallet was connected before
      const activeAccount = await newWallet.client.getActiveAccount();
      if (activeAccount) {
        const userPkh = await newWallet.getPKH();
        setUserAddress(userPkh);
        const balance = await Tezos.tz.getBalance(userPkh);
        setUserBalance(balance.toNumber());
      }
    }
  });
</script>

<div class="buttons">
  <button class="button" on:click={connectWallet}>
    <span>
      <i class="fas fa-wallet" />&nbsp; Connect with wallet
    </span>
  </button>
  <button class="button" disabled={loadingNano} on:click={connectNano}>
    {#if loadingNano}
      <span>
        <i class="fas fa-spinner fa-spin" />&nbsp; Loading, please wait
      </span>
    {:else}
      <span>
        <i class="fab fa-usb" />&nbsp; Connect with Ledger Nano
      </span>
    {/if}
  </button>
</div>
