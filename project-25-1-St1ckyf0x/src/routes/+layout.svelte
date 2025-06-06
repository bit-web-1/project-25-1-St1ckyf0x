<script lang="ts">
  import { onMount } from 'svelte';

  let videoEl: HTMLVideoElement | null = null;

  onMount(() => {
    if (videoEl) {
      videoEl.playbackRate = 0.75; // slow down video playback speed to half
    }
  });
</script>

<header>
  <nav>
    <a href="/" class="nav-link">Home</a>
    <img src="/eldenring-title.png" alt="Elden Ring Title" class="nav-logo" />
    <a href="/bosses" class="nav-link">Bosses</a>
  </nav>
</header>

<div class="video-banner">
  <video autoplay muted loop playsinline bind:this={videoEl}>
    <source src="/elden-ring-header-animated.webm" type="video/webm" />
    Your browser does not support the video tag.
  </video>
</div>


<!-- Spacer pushes content down -->
<div class="banner-spacer"></div>

<main>
  <slot />
</main>

<style>
  @font-face {
    font-family: 'Mantinia';
    src: url('/fonts/Mantinia.otf') format('opentype');
    font-weight: normal;
    font-style: normal;
  }

  :global(body) {
    font-family: 'Mantinia', serif;
    background-color: #0e0e0e;
    color: #e0e0e0;
    margin: 0;
    padding: 0;
    text-transform: lowercase;
  }

  header {
    background-color: rgba(14, 14, 14, 0.85);
    padding: 1rem 2rem;
    position: relative;
    z-index: 3;
  }

  nav {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .nav-link {
    color: #d4af37;
    text-decoration: none;
    font-weight: bold;
    font-size: 1.1rem;
    flex-grow: 1;
    text-align: center;
  }

  .nav-link:first-child {
    text-align: left;
    flex-grow: 0;
  }

  .nav-link:last-child {
    text-align: right;
    flex-grow: 0;
  }

  .nav-logo {
    height: 65px;
    object-fit: contain;
  }

  .video-banner {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 1000px; /* fixed banner height */
    overflow: hidden;
    z-index: 0;
  }

  .video-banner video {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  /* Spacer div same height as banner */
  .banner-spacer {
    height: 600px;
  }

  main {
    position: relative;
    z-index: 5;
    margin: 300px auto 4rem; /* overlap by half banner height */
    padding: 2rem 1rem 2rem;
    background-color: #0e3331c2;
    border-radius: 1px;
    box-shadow: 0 8px 24px rgba(0,0,0,0.8);
  }
</style>
