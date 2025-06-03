<script>
  import { nightlords } from '$lib/nightlords.js';
</script>

<h1>Nightlords Bosses</h1>

<ul>
  {#each nightlords as boss}
    <li>
      <a href={`/nightlords/${boss.id}`}>
        <strong>{boss.name}</strong> — Weakness: {boss.weakness}
      </a>
    </li>
  {/each}
</ul>

<style>
  h1 {
    font-size: 2rem;
    margin-bottom: 1rem;
  }

  ul {
    list-style: none;
    padding: 0;
  }

  li {
    margin-bottom: 0.75rem;
  }

  a {
    text-decoration: none;
    color: #0070f3;
  }

  a:hover {
    text-decoration: underline;
  }
</style>
