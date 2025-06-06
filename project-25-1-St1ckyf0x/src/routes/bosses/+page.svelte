<script>
  import { bosses } from '$lib/bosses.js';
</script>

<h1>Elden Ring: Boss Overview</h1>

<ul>
  {#each bosses as boss}
    <li>
      <h2><a href={`/bosses/${boss.id}`}>{boss.name}</a></h2>
      <p><strong>Weakness:</strong> {boss.weakness}</p>
      <p>{boss.strategy.slice(0, 100)}...</p>
    </li>
  {/each}
</ul>

<style>
  h1 {
    font-size: 2.5rem;
    margin-bottom: 2rem;
  }

  li {
    margin-bottom: 1.5rem;
    border-bottom: 1px solid #ccc;
    padding-bottom: 1rem;
  }

  a {
    color: #0070f3;
    text-decoration: none;
  }

  a:hover {
    text-decoration: underline;
  }

  p {
    margin: 0.25rem 0;
  }
</style>
