<script lang="ts">
  // pulling in current page store so we can get the boss ID
  import { page } from '$app/stores';
  import { get } from 'svelte/store';

  //our boss type, mostly from the API's response structure
  type Boss = {
    id: string;
    name: string;
    image: string;
    description?: string;
    location?: string;
    healthPoints?: string;
    drops?: string[];
  };

  // app state - boss we're showing, loading state and any errors
  let boss: Boss | null = null;
  let isLoading = false;
  let error: string | null = null;

  // Reactive id from URL param
  $: id = get(page).params.id;

  // Fetch boss data whenever id changes
  $: if (id) {
    fetchBoss(id);
  }

  //fetching the boss data
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

<div class="scroll-hint">Scroll down for content ↓</div>

<!-- link back to the boss list-->
<a href="/bosses" class="back-link">← Back to Boss List</a>

{#if isLoading}
  <p>Loading boss data...</p>
  <!--handles errors-->
{:else if error}
  <h1>Error</h1>
  <p>{error}</p>
  <!--main render when we have a boss-->
{:else if boss}
  <div class="boss-container">
    <div class="boss-text">
      <h1>{boss.name}</h1>
  <!--if we have a description, display it-->
      {#if boss.description}
        <h2>Description</h2>
        <p>{boss.description}</p>
      {/if}
      <!--location info if its available-->
      {#if boss.location}
        <p><strong>Location:</strong> {boss.location}</p>
      {/if}
      <!--health points, some bosses don't have-->
      {#if boss.healthPoints}
        <p><strong>Health:</strong> {boss.healthPoints}</p>
      {/if}
  <!--loot drops-->
      {#if boss.drops && boss.drops.length > 0}
        <h2>Drops</h2>
        <ul class="drops-list">
          {#each boss.drops as drop}
            <li>{drop}</li>
          {/each}
        </ul>
      {/if}
    </div>
    <!--boss image-->
    {#if boss.image}
      <img src={boss.image} alt={`Image of ${boss.name}`} class="boss-image" />
    {/if}
  </div>
  <!--shows if nothing worked-->
{:else}
  <h1>Boss not found</h1>
  <p>Sorry, we couldn't find an Elden Ring boss with that ID.</p>
{/if}


<style>
      .scroll-hint {
    font-weight: bold;
    font-size: 1.25rem;
    text-align: center;
    padding: 1rem 0;
    color: #d4af37; /* gold-ish color */
  }
  
  h1 {
    font-size: 2rem;
    margin-bottom: 1rem;
  }

  h2 {
    margin-top: 1.5rem;
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

  .boss-container {
    display: flex;
    align-items: flex-start;
    gap: 2rem;
  }

  .boss-text {
    flex: 1;
  }

  .boss-image {
    max-width: 500px;
    width: 100%;
    border-radius: 6px;
    object-fit: contain;
  }

  .drops-list {
  list-style: none;
  padding: 0;
  margin: 0.5rem 0; /* Added vertical spacing around the list */
}

.drops-list li {
  margin-bottom: 0.5rem;    /* Adds spacing between each drop */
  padding-left: 1.2rem;     /* Creates space for a custom bullet */
  position: relative;       /* Needed for positioning the custom bullet */
}

.drops-list li::before {
  content: '•';             /* Custom bullet character */
  position: absolute;       /* Position it inside the li */
  left: 0;                  /* Align left inside the li */
  color: #d4af37;           /* Styled gold color */
  font-weight: bold;        /* Make it stand out */
}


</style>
