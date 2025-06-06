<script>
    import { bosses } from '$lib/bosses.js';
    import { page } from '$app/stores';
  
    let id;
    let boss;
  
    $: id = $page.params.id;
    $: boss = bosses.find(b => b.id === id);
  </script>
  
  {#if boss}
    <h1>{boss.name}</h1>
    <p><strong>Weakness:</strong> {boss.weakness}</p>
  
    <h2>Strategy</h2>
    <pre>{boss.strategy}</pre>
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
      font-size: 1.25rem;
    }
  
    pre {
      white-space: pre-wrap;
      background: #f8f8f8;
      padding: 1rem;
      border-radius: 0.5rem;
      font-family: inherit;
    }
  </style>
  