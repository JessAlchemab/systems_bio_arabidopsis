<script>
  import { currentPage } from "../stores.ts";

  export let isOpen = true;

  function setPage(page) {
    currentPage.set(page);
  }
</script>

<div class="sidebar-container">
  <div class="sidebar" class:open={isOpen}>
    <h2>Pages</h2>
    <ul>
      <li>
        <a href="#" on:click={() => setPage("500-fold")}>500-fold Network</a>
      </li>
      <li>
        <a href="#" on:click={() => setPage("100-fold")}>100-fold Network</a>
      </li>
      <li><a href="#" on:click={() => setPage("info")}>About</a></li>
    </ul>
  </div>
</div>

<style>
  .sidebar-container {
    position: fixed;
    top: 6rem;
    left: 0;
    bottom: 0;
    z-index: 1000;
  }

  .sidebar {
    width: 250px;
    height: 100%;
    background-color: #476268;
    transform: translateX(-100%);
    transition: transform 0.3s ease-in-out;
    padding: 20px;
    box-sizing: border-box;
  }

  .sidebar.open {
    transform: translateX(0);
  }

  h2 {
    margin-top: 0;
    color: #faf9f9;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    margin-bottom: 10px;
  }

  a {
    color: rgb(171, 183, 186);
  }
</style>
