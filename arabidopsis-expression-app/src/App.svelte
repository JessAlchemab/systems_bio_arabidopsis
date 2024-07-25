<script lang="ts">
  import { Router, Route, Link } from "svelte-routing";
  import Navbar from "./components/Navbar.svelte";
  import Sidebar from "./components/Sidebar.svelte";
  import Graph from "./components/Graph.svelte";
  import Graph100 from "./components/100Graph.svelte";
  import NetworkInformation from "./components/NetworkInformation.svelte";
  import { currentPage } from "./stores.ts";

  let sidebarOpen = true;
</script>

<Navbar />
<Sidebar bind:isOpen={sidebarOpen} />
<div class="main">
  {#if $currentPage === "500-fold"}
    <Graph />
  {:else if $currentPage === "100-fold"}
    <Graph100 />
  {:else if $currentPage === "info"}
    <NetworkInformation />
  {/if}
</div>

<style>
  .main {
    width: calc(80vw - 250px);
    height: calc(80vh - 5rem);
  }
</style>
