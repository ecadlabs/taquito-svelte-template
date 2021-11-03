<script lang="ts">
  import { onMount } from "svelte";
  import { TezosToolkit } from "@taquito/taquito";
  import type { BeaconWallet } from "@taquito/beacon-wallet";
  import { NetworkType } from "@airgap/beacon-sdk";
  import ConnectButton from "./components/ConnectButton.svelte";
  import DisconnectButton from "./components/DisconnectButton.svelte";
  import Transfers from "./components/Transfers.svelte";
  import UpdateContract from "./components/UpdateContract.svelte";
  import WalletTab from "./components/WalletTab.svelte";

  let Tezos: TezosToolkit;
  let wallet: BeaconWallet;
  const contractAddress: string = "KT1WiPWNcBMcXJButkkvroRGkzs45n3iZ13c";
  let userAddress: string;
  let userBalance: number;
  let activeTab = "transfer";

  const rpcUrl = "https://hangzhounet.api.tez.ie";

  const setWallet = (w: BeaconWallet | undefined) => (wallet = w);
  const setUserAddress = (a: string | undefined) => (userAddress = a);
  const setUserBalance = (b: number | undefined) => (userBalance = b);

  onMount(async () => {
    Tezos = new TezosToolkit(rpcUrl);
  });
</script>

<style lang="scss">
  @import "./styles/settings.scss";

  .main-box {
    margin: auto;

    .title {
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      align-items: center;
      color: $main-color;
    }

    h1 {
      color: $main-color;
      text-align: left;
    }
  }
</style>

<WalletTab
  {userAddress}
  {wallet}
  {setWallet}
  {setUserAddress}
  {setUserBalance}
/>
<main>
  {#if Tezos}
    <div class="main-box">
      <div class="title">
        <h1>Taquito Svelte Template</h1>
        <a
          href="https://app.netlify.com/start/deploy?repository=https://github.com/ecadlabs/taquito-boilerplate"
        >
          <img
            src="https://www.netlify.com/img/deploy/button.svg"
            alt="netlify-button"
          />
        </a>
      </div>
      {#if userAddress}
        <div id="tabs">
          <div
            id="transfer"
            class:active={activeTab === "transfer"}
            on:click={() => (activeTab = "transfer")}
          >
            Make a transfer
          </div>
          <div
            id="contract"
            class:active={activeTab === "contract"}
            on:click={() => (activeTab = "contract")}
          >
            Interact with a contract
          </div>
        </div>
        <div id="dialog">
          <div id="content">
            {#if activeTab === "transfer"}
              <div id="transfers">
                <Transfers {Tezos} {userAddress} {setUserBalance} />
              </div>
            {:else if activeTab === "contract"}
              <UpdateContract
                {contractAddress}
                {userAddress}
                {setUserBalance}
                {Tezos}
              />
            {/if}
            <p>
              <i class="far fa-file-code" />&nbsp;
              <a
                href={`https://better-call.dev/hangzhounet/${contractAddress}/operations`}
                target="_blank"
                rel="noopener noreferrer"
              >
                {contractAddress}
              </a>
            </p>
            <p>
              <i class="far fa-address-card" />&nbsp; {userAddress}
            </p>
            <p>
              <i class="fas fa-piggy-bank" />&nbsp;
              {(userBalance / 1000000).toLocaleString("en-US")} ꜩ
            </p>
          </div>
          <DisconnectButton
            {wallet}
            {setWallet}
            {setUserAddress}
            {setUserBalance}
          />
        </div>
      {:else}
        <div id="dialog">
          <header>Welcome to Taquito Svelte Template!</header>
          <div id="content">
            <p>Hello!</p>
            <p>
              This is a template Tezos dApp built using Taquito. It's a starting
              point for you to hack on and build your own dApp for Tezos.
              <br />
              If you have not done so already, go to the{" "}
              <a
                href="https://github.com/ecadlabs/taquito-boilerplate"
                target="_blank"
                rel="noopener noreferrer"
              >
                Taquito Svelte Template Github page
              </a>{" "}
              and click the <em>"Use this template"</em> button.
            </p>
            <p>Go forth and Tezos!</p>
          </div>
          <ConnectButton
            {setWallet}
            {wallet}
            {setUserAddress}
            {Tezos}
            {setUserBalance}
          />
        </div>
      {/if}
      <div id="footer">
        <img src="built-with-taquito.png" alt="Built with Taquito" />
      </div>
    </div>
  {/if}
</main>
