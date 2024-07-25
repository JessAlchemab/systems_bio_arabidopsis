<script lang="ts">
  import Logo from "../assets/logo.svelte";
</script>

<nav>
  <div class="nav-content">
    <Logo />
    <h1>Arbidopsis Germination Viewer</h1>
  </div>
</nav>

<style>
  nav {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    height: 6rem;
    background-color: rgb(144, 154, 157);
    color: #40a798;
    z-index: 999;
  }

  .nav-content {
    display: flex;
    align-items: center;
    height: 100%;
    padding: 0 20px;
  }

  h1 {
    margin: 0;
    margin-left: 1rem;
    font-size: 1.5em;
    color: #414a50;
  }
</style>
