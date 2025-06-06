<script lang="ts">
  import { onMount } from 'svelte';

  type Boss = {
    id: string;
    name: string;
    description: string;
    location: string;
  };

  let bosses: Boss[] = [];
  let isLoading = true;
  let error: string | null = null;

  let currentPage = 1;
  const limit = 20; // bosses per page
  let totalPages = 1;

  async function fetchBosses(page = 1) {
    isLoading = true;
    error = null;

    try {
      const res = await fetch(`https://eldenring.fanapis.com/api/bosses?page=${page}&limit=${limit}`);
      if (!res.ok) throw new Error('Failed to fetch bosses');

      const data = await res.json();
      bosses = data.data;
      
      // total count from API response for pagination
      const totalBosses = data.total; 
      totalPages = Math.ceil(totalBosses / limit);
      currentPage = page;
    } catch (err) {
      error = 'Failed to load boss data.';
    } finally {
      isLoading = false;
    }
  }

  onMount(() => {
    fetchBosses(currentPage);
  });

  function goToPage(page: number) {
    if (page < 1 || page > totalPages) return;
    fetchBosses(page);
  }
</script>

<h1>Elden Ring: Boss Overview</h1>

{#if isLoading}
  <p>Loading bosses...</p>
{:else if error}
  <p>{error}</p>
{:else}
  <ul>
    {#each bosses as boss}
      <li>
        <h2><a href={`/bosses/${boss.id}`}>{boss.name}</a></h2>
        <p><strong>Location:</strong> {boss.location}</p>
        <p>{boss.description.length > 100 ? boss.description.slice(0, 100) + '...' : boss.description}</p>
      </li>
    {/each}
  </ul>

  <nav class="pagination">
    <button on:click={() => goToPage(currentPage - 1)} disabled={currentPage === 1}>Previous</button>

    {#each Array(totalPages) as _, i}
      <button
        class:active={currentPage === i + 1}
        on:click={() => goToPage(i + 1)}>
        {i + 1}
      </button>
    {/each}

    <button on:click={() => goToPage(currentPage + 1)} disabled={currentPage === totalPages}>Next</button>
  </nav>
{/if}

<style>
  h1 {
    font-size: 2.5rem;
    margin-bottom: 2rem;
    text-transform: lowercase;
  }

  ul {
    list-style: none;
    padding: 0;
  }

  li {
    margin-bottom: 1.5rem;
    border-bottom: 1px solid #ccc;
    padding-bottom: 1rem;
    text-transform: lowercase;
  }

  a {
    color: #d4af37;
    text-decoration: none;
  }

  a:hover {
    text-decoration: underline;
  }

  p {
    margin: 0.25rem 0;
  }

  .pagination {
    margin-top: 1.5rem;
    display: flex;
    gap: 0.5rem;
    flex-wrap: wrap;
  }

  .pagination button {
    background: none;
    border: 1px solid #d4af37;
    padding: 0.4rem 0.8rem;
    cursor: pointer;
    text-transform: lowercase;
    color: #d4af37;
  }

  .pagination button:disabled {
    opacity: 0.4;
    cursor: default;
  }

  .pagination button.active {
    background-color: #d4af37;
    color: black;
  }
</style>
