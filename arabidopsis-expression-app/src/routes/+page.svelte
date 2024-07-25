<!-- src/routes/+page.svelte -->
<script>
  import Navbar from '$lib/components/Navbar.svelte';
  import Sidebar from '$lib/components/Sidebar.svelte';
  import Graph from '$lib/components/Graph.svelte';

  let sidebarOpen = false;
</script>

<Navbar />
<Sidebar bind:isOpen={sidebarOpen} />
<Graph />
