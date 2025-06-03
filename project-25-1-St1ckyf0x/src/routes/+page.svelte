<script>
  let name = ''
</script>

<h1>Welcome to My SvelteKit Site</h1>

<input bind:value={name} placeholder="What's your name?" />
<p>Hello {name || 'stranger'}!</p>
