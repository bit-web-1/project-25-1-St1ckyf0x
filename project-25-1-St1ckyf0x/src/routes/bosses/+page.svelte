<script lang="ts">
  import { onMount } from 'svelte';

  // Boss data shape from API
  type Boss = {
    id: string;
    name: string;
    image: string;
    description: string;
    location: string;
    healthPoints?: string;   // Added
    drops?: string[];        // Added
  };

  // State variables
  let bosses: Boss[] = [];
  let currentPage = 0; // zero-based for API calls
  const perPage = 50; // how many bosses per page
  let totalPages = 1;
  let totalBosses = 0;

  let isLoading = false;
  let minLoading = false;
  let error: string | null = null;

  // Fetch one page of bosses from API
  async function fetchBossesPage(page: number) {
    isLoading = true;
    minLoading = true;
    error = null;

      // Minimum loading timer promise
  const minLoadingTimer = new Promise((resolve) => {
    setTimeout(() => {
      minLoading = false;
      resolve(null);
    }, 3000); // 3 seconds minimum loading screen
  });

    try {
      const res = await fetch(`https://eldenring.fanapis.com/api/bosses?limit=${perPage}&page=${page}`);
      if (!res.ok) throw new Error(`Failed to fetch page ${page + 1}`);

      const data = await res.json();
      bosses = data.data;
      totalBosses = data.total;
      totalPages = Math.ceil(totalBosses / perPage);
      currentPage = page;
      
    } catch (e) {
      error = (e as Error).message || 'Failed to load bosses.';
      bosses = [];
    } finally {
      isLoading = false;
      await minLoadingTimer;
    }
  }

  // Trigger fetch if page is different
  function goToPage(page: number) {
    if (page !== currentPage) {
      fetchBossesPage(page);
    }
  }

  // Fetch first page on load
  onMount(() => {
    fetchBossesPage(0);
  });
</script>

<div class="scroll-hint">Scroll down for content ↓</div>

<div class="header">
  <h1>Elden Ring Boss Overview and Location</h1>
</div>

{#if isLoading || minLoading}
  <div class="loading-screen" aria-label="Loading">
    <img src="/elden-ring-loading-logo.png" alt="Elden Ring Loading Logo" class="loading-logo" />
  </div>
{:else if error}
  <p class="error">{error}</p>
{:else if bosses.length === 0}
  <p>No bosses found.</p>
{:else}
  <!-- List bosses -->
<ul class="boss-list">
  {#each bosses as boss}
    <li class="boss-item">
      <div class="boss-content">
        <div class="boss-text">
          <h2><a href={`/bosses/${boss.id}`}>{boss.name}</a></h2>
          <p><strong>Location:</strong> {boss.location}</p>
          <p>{boss.description.length > 100 ? boss.description.slice(0, 100) + '...' : boss.description}</p>
        </div>
        {#if boss.image}
          <img src={boss.image} alt={`Image of ${boss.name}`} class="boss-image" />
        {/if}
      </div>
    </li>
  {/each}
</ul>




  <!-- Pagination buttons -->
  <div class="pagination" role="navigation" aria-label="Pagination Navigation">
    {#each Array(totalPages) as _, index}
      <button
        class:selected={currentPage === index}
        on:click={() => goToPage(index)}
        aria-current={currentPage === index ? 'page' : undefined}
        disabled={currentPage === index}
        aria-label={`Page ${index + 1}`}
      >
        {index + 1}
      </button>
    {/each}
  </div>
{/if}

<style>
    .scroll-hint {
    font-weight: bold;
    font-size: 1.25rem;
    text-align: center;
    padding: 1rem 0;
    color: #d4af37; /* gold-ish color */
  }
  
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

  .pagination {
    margin-top: 1.5rem;
    display: flex;
    gap: 0.5rem;
    flex-wrap: wrap;
  }

  .pagination button {
    padding: 0.4rem 0.8rem;
    border: 1px solid #d4af37;
    border-radius: 4px;
    background-color: transparent;
    color: #d4af37;
    cursor: pointer;
    font-weight: 600;
    transition: background-color 0.3s, color 0.3s;
  }

  .pagination button:hover:not(:disabled) {
    background-color: #d4af37;
    color: black;
  }

  .pagination button.selected,
  .pagination button:disabled {
    background-color: #d4af37;
    color: black;
    cursor: default;
    pointer-events: none;
  }

  .loading-screen {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.8);
  backdrop-filter: blur(6px);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
}

.loading-logo {
  width: 150px;
  height: 150px;
  animation: pulse 2s infinite ease-in-out;
}

@keyframes pulse {
  0%, 100% {
    transform: scale(1);
    filter: drop-shadow(0 0 5px #d4af37);
  }
  50% {
    transform: scale(1.1);
    filter: drop-shadow(0 0 15px #d4af37);
  }
}

.boss-item {
  padding: 1rem 0;
  border-bottom: 1px solid #ddd;
}

.boss-content {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 1rem;
}

.boss-text {
  flex: 1;
}

.boss-image {
  max-width: 150px;
  width: 100%;
  border-radius: 6px;
  object-fit: cover;
}



</style>
