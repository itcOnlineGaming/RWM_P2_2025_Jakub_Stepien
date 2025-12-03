<script lang="ts">
	import favicon from '$lib/assets/favicon.svg';

	import { onMount } from 'svelte';
  
  onMount(() => {
    // Initialize emotion counts if not present
    if (!localStorage.getItem('emotionCounts')) {
      const emotions = [
        'joy', 'trust', 'fear', 'surprise',
        'sadness', 'disgust', 'anger', 'anticipation',
        'optimism', 'love', 'submission', 'awe',
        'disapproval', 'remorse', 'contempt', 'aggressiveness',
        'serenity', 'acceptance', 'apprehension', 'distraction',
        'pensiveness', 'boredom', 'annoyance', 'interest'
      ];
      
      const counts: any = {};
      emotions.forEach(emotion => {
        counts[emotion] = { name: emotion, count: 0 };
      });
      
      localStorage.setItem('emotionCounts', JSON.stringify(counts));
    }
  });

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  
  :global(*, *::before, *::after) {
    box-sizing: inherit;
  }
</style>

{@render children()}