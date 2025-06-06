<script lang="ts">
  import { onMount } from 'svelte';

  type Boss = {
    id: string;
    name: string;
    description: string;
    location: string;
  };

  let allBosses: Boss[] = [];
  let filteredBosses: Boss[] = [];
  let searchInput = '';

  let isLoading = false;
  let error: string | null = null;

  async function fetchAllBosses() {
    let bosses: Boss[] = [];
    let page = 1;
    const perPage = 50;
    let total = Infinity; // we'll update this once we know it

    try {
      while (bosses.length < total && page < 20) { // safety limit of 20 pages
        const res = await fetch(`https://eldenring.fanapis.com/api/bosses?limit=${perPage}&page=${page}`);
        if (!res.ok) throw new Error(`Failed to fetch page ${page}`);

        const data = await res.json();
        if (!data || !data.data || data.data.length === 0) break;

        bosses = bosses.concat(data.data);
        total = data.total;
        page++;
      }
    } catch (e) {
      console.error('Error fetching bosses:', e);
      throw e;
    }

    return bosses;
  }

  onMount(async () => {
    isLoading = true;
    error = null;

    try {
      allBosses = await fetchAllBosses();
      filteredBosses = allBosses;
    } catch (e) {
      error = 'Failed to load boss data.';
    } finally {
      isLoading = false;
    }
  });

  // Filter bosses locally whenever searchInput changes
  $: filteredBosses = searchInput.trim() === ''
    ? allBosses
    : allBosses.filter(boss =>
        boss.name.toLowerCase().includes(searchInput.toLowerCase())
      );
</script>

<div class="header">
  <h1>Elden Ring Boss Overview</h1>
  <input
    type="text"
    placeholder="Search bosses..."
    bind:value={searchInput}
    aria-label="Search bosses"
  />
</div>

{#if isLoading}
  <p>Loading bosses... (this may take a few seconds)</p>
{:else if error}
  <p class="error">{error}</p>
{:else if filteredBosses.length === 0}
  <p>No bosses found.</p>
{:else}
  <ul class="boss-list">
    {#each filteredBosses as boss}
      <li>
        <h2><a href={`/bosses/${boss.id}`}>{boss.name}</a></h2>
        <p><strong>Location:</strong> {boss.location}</p>
        <p>{boss.description.length > 100 ? boss.description.slice(0, 100) + '...' : boss.description}</p>
      </li>
    {/each}
  </ul>
{/if}

<style>
  .header {
    display: flex;
    align-items: center;
    gap: 1rem;
    margin-bottom: 1.5rem;
  }

  h1 {
    flex-grow: 1;
    font-size: 2rem;
    text-transform: lowercase;
  }

  input[type="text"] {
    padding: 0.5rem;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    width: 200px;
  }

  ul.boss-list {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  ul.boss-list li {
    border-bottom: 1px solid #ddd;
    padding: 1rem 0;
  }

  ul.boss-list li h2 {
    margin: 0 0 0.25rem 0;
    color: #d4af37;
    text-transform: lowercase;
  }

  ul.boss-list li a {
    color: inherit;
    text-decoration: none;
  }

  ul.boss-list li a:hover {
    text-decoration: underline;
  }

  .error {
    color: red;
  }
</style>
