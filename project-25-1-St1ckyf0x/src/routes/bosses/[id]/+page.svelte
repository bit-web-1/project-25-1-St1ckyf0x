<script lang="ts">
  import { page } from '$app/stores';
  import { get } from 'svelte/store';

  type Boss = {
    id: string;
    name: string;
    weakness?: string;
    strategy?: string;
    description?: string;
    location?: string;
  };

  let boss: Boss | null = null;
  let isLoading = false;
  let error: string | null = null;

  // Reactive id from URL param
  $: id = get(page).params.id;

  // Fetch boss data whenever id changes
  $: if (id) {
    fetchBoss(id);
  }

  async function fetchBoss(id: string) {
    isLoading = true;
    error = null;
    boss = null;

    try {
      const res = await fetch(`https://eldenring.fanapis.com/api/bosses/${id}`);
      if (!res.ok) {
        throw new Error('Boss not found');
      }
      const data = await res.json();
      boss = data.data;
    } catch (err) {
      error = 'Failed to load boss data or boss not found.';
    } finally {
      isLoading = false;
    }
  }
</script>

<a href="/bosses" class="back-link">← Back to Boss List</a>

{#if isLoading}
  <p>Loading boss data...</p>
{:else if error}
  <h1>Error</h1>
  <p>{error}</p>
{:else if boss}
  <h1>{boss.name}</h1>
  {#if boss.weakness}
    <p><strong>Weakness:</strong> {boss.weakness}</p>
  {/if}
  {#if boss.strategy}
    <h2>Strategy</h2>
    <pre>{boss.strategy}</pre>
  {/if}
  {#if boss.description}
    <h2>Description</h2>
    <p>{boss.description}</p>
  {/if}
  {#if boss.location}
    <p><strong>Location:</strong> {boss.location}</p>
  {/if}
{:else}
  <h1>Boss not found</h1>
  <p>Sorry, we couldn't find an Elden Ring boss with that ID.</p>
{/if}

<style>
  h1 {
    font-size: 2rem;
    margin-bottom: 1rem;
  }

  h2 {
    margin-top: 1.5rem;
  }

  pre {
    white-space: pre-wrap;
    background: #f8f8f8;
    padding: 1rem;
    border-radius: 0.5rem;
    font-family: inherit;
  }

  p {
    margin: 0.5rem 0;
  }

  .back-link {
    display: inline-block;
    margin-bottom: 1rem;
    color: #d4af37;
    text-decoration: none;
    font-weight: bold;
  }

  .back-link:hover {
    text-decoration: underline;
  }
</style>
