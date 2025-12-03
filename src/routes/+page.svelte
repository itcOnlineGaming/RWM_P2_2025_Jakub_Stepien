<!-- src/routes/+page.svelte -->
<script lang="ts">
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';
  
  interface EmotionData {
    name: string;
    count: number;
  }
  
  interface EmotionMap {
    [key: string]: EmotionData;
  }
  
  // Plutchik's 8 basic emotions
  const emotions = [
    'joy', 'trust', 'fear', 'surprise',
    'sadness', 'disgust', 'anger', 'anticipation'
  ];
  
  // Secondary emotions (dyads)
  const secondaryEmotions = [
    'optimism', 'love', 'submission', 'awe',
    'disapproval', 'remorse', 'contempt', 'aggressiveness'
  ];
  
  // Tertiary emotions
  const tertiaryEmotions = [
    'serenity', 'acceptance', 'apprehension', 'distraction',
    'pensiveness', 'boredom', 'annoyance', 'interest'
  ];
  
  const allEmotions = [...emotions, ...secondaryEmotions, ...tertiaryEmotions];
  
  let emotionCounts: EmotionMap = {};
  let selectedEmotion: string | null = null;
  
  onMount(() => {
    loadEmotionCounts();
  });
  
  function loadEmotionCounts() {
    // Load emotion counts from localStorage
    const stored = localStorage.getItem('emotionCounts');
    if (stored) {
      emotionCounts = JSON.parse(stored);
    } else {
      // Initialize all emotions with count 0
      allEmotions.forEach(emotion => {
        emotionCounts[emotion] = { name: emotion, count: 0 };
      });
    }
  }
  
  function getEmotionColor(emotion: string): string {
  const count = emotionCounts[emotion]?.count || 0;
  const isSelected = selectedEmotion === emotion;

  const colorMap: { [key: string]: string } = {
    'joy': '#FFD700',
    'serenity': '#FFF8DC',
    'trust': '#90EE90',
    'acceptance': '#E0F0E0',
    'fear': '#32CD32',
    'apprehension': '#B0F0B0',
    'surprise': '#87CEEB',
    'distraction': '#D0E8F0',
    'sadness': '#4169E1',
    'pensiveness': '#A0C0E0',
    'disgust': '#9370DB',
    'boredom': '#D0C0E0',
    'anger': '#FF6347',
    'annoyance': '#FFB0A0',
    'anticipation': '#FFA500',
    'interest': '#FFD8A0',
    'optimism': '#FFEC8B',
    'love': '#FFB6C1',
    'submission': '#98FB98',
    'awe': '#7FFFD4',
    'disapproval': '#8A7FB5',
    'remorse': '#6495ED',
    'contempt': '#BC8F8F',
    'aggressiveness': '#FF8C69'
  };

  const baseColor = colorMap[emotion] || '#ffffff';

  // Convert hex to RGB
  const r = parseInt(baseColor.slice(1, 3), 16);
  const g = parseInt(baseColor.slice(3, 5), 16);
  const b = parseInt(baseColor.slice(5, 7), 16);

  // Progress = 0 → white, 1 → full color
  const progress = Math.min(count / 10, 1);

  // Apply selection boost
  const boost = isSelected ? 0.2 : 0;

  const p = Math.min(progress + boost, 1);

  // Blend white → color
  const rr = Math.round(255 + (r - 255) * p);
  const gg = Math.round(255 + (g - 255) * p);
  const bb = Math.round(255 + (b - 255) * p);

  return `rgb(${rr}, ${gg}, ${bb})`;
}
  
  function getStrokeWidth(emotion: string): number {
    return selectedEmotion === emotion ? 3 : 1.5;
  }
  
  function selectEmotion(emotion: string) {
    selectedEmotion = emotion;
  }
  
  function handleSubmit() {
    if (selectedEmotion) {
      goto(`/emotion/${selectedEmotion}`);
    }
  }
  
  // Listen for when user returns from emotion detail page
  onMount(() => {
    loadEmotionCounts();
    
    // Reload counts when page becomes visible again
    const handleVisibilityChange = () => {
      if (!document.hidden) {
        loadEmotionCounts();
        selectedEmotion = null; // Clear selection when returning
      }
    };
    
    document.addEventListener('visibilitychange', handleVisibilityChange);
    
    return () => {
      document.removeEventListener('visibilitychange', handleVisibilityChange);
    };
  });
</script>

<main>
  <div class="container">
    <h1>What emotion are you feeling right now?</h1>
    
    <div class="wheel-container">
      <svg viewBox="0 0 400 400" class="emotion-wheel">
        <!-- Center circle -->
        <circle cx="200" cy="200" r="180" fill="#F4A460" opacity="0.3"/>
        
        <!-- Core emotions (8 petals) -->
        {#each emotions as emotion, i}
          {@const angle = (i * 45) - 90}
          {@const x = 200 + 100 * Math.cos(angle * Math.PI / 180)}
          {@const y = 200 + 100 * Math.sin(angle * Math.PI / 180)}
          
          <ellipse
            cx={x}
            cy={y}
            rx="45"
            ry="65"
            fill={getEmotionColor(emotion)}
            stroke={selectedEmotion === emotion ? '#FF6B00' : '#333'}
            stroke-width={getStrokeWidth(emotion)}
            transform="rotate({angle} {x} {y})"
            class="emotion-petal"
            class:selected={selectedEmotion === emotion}
            on:click={() => selectEmotion(emotion)}
            on:keydown={(e) => e.key === 'Enter' && selectEmotion(emotion)}
            role="button"
            tabindex="0"
          />
          <text
            x={x}
            y={y + 5}
            text-anchor="middle"
            class="emotion-label"
            class:selected={selectedEmotion === emotion}
            on:click={() => selectEmotion(emotion)}
          >
            {emotion}
          </text>
        {/each}
        
        <!-- Secondary emotions (between petals) -->
        {#each secondaryEmotions as emotion, i}
          {@const angle = (i * 45 + 22.5) - 90}
          {@const x = 200 + 145 * Math.cos(angle * Math.PI / 180)}
          {@const y = 200 + 145 * Math.sin(angle * Math.PI / 180)}
          
          <ellipse
            cx={x}
            cy={y}
            rx="30"
            ry="45"
            fill={getEmotionColor(emotion)}
            stroke={selectedEmotion === emotion ? '#FF6B00' : '#333'}
            stroke-width={selectedEmotion === emotion ? 2 : 1}
            transform="rotate({angle} {x} {y})"
            class="emotion-petal secondary"
            class:selected={selectedEmotion === emotion}
            on:click={() => selectEmotion(emotion)}
            on:keydown={(e) => e.key === 'Enter' && selectEmotion(emotion)}
            role="button"
            tabindex="0"
          />
          <text
            x={x}
            y={y + 3}
            text-anchor="middle"
            class="emotion-label small"
            class:selected={selectedEmotion === emotion}
            on:click={() => selectEmotion(emotion)}
          >
            {emotion}
          </text>
        {/each}
        
        <!-- Tertiary emotions (milder versions) -->
        {#each tertiaryEmotions as emotion, i}
          {@const angle = (i * 45) - 90}
          {@const x = 200 + 60 * Math.cos(angle * Math.PI / 180)}
          {@const y = 200 + 60 * Math.sin(angle * Math.PI / 180)}
          
          <ellipse
            cx={x}
            cy={y}
            rx="25"
            ry="35"
            fill={getEmotionColor(emotion)}
            stroke={selectedEmotion === emotion ? '#FF6B00' : '#333'}
            stroke-width={selectedEmotion === emotion ? 1.5 : 1}
            transform="rotate({angle} {x} {y})"
            class="emotion-petal tertiary"
            class:selected={selectedEmotion === emotion}
            on:click={() => selectEmotion(emotion)}
            on:keydown={(e) => e.key === 'Enter' && selectEmotion(emotion)}
            role="button"
            tabindex="0"
          />
          <text
            x={x}
            y={y + 2}
            text-anchor="middle"
            class="emotion-label tiny"
            class:selected={selectedEmotion === emotion}
            on:click={() => selectEmotion(emotion)}
          >
            {emotion}
          </text>
        {/each}
      </svg>
    </div>
    
    {#if selectedEmotion}
      <div class="selection-indicator">
        You selected: <strong>{selectedEmotion}</strong>
      </div>
    {/if}
    
    <button 
      class="submit-btn" 
      on:click={handleSubmit}
      disabled={!selectedEmotion}
    >
      Submit
    </button>
  </div>
</main>

<style>
  main {
    min-height: 100vh;
    background: linear-gradient(135deg, #E89968 0%, #D88850 100%);
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 2rem;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  }
  
  .container {
    max-width: 600px;
    width: 100%;
  }
  
  h1 {
    color: white;
    text-align: center;
    font-size: 1.5rem;
    margin-bottom: 2rem;
    font-weight: 400;
  }
  
  .wheel-container {
    background: white;
    border-radius: 50%;
    padding: 1rem;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  }
  
  .emotion-wheel {
    width: 100%;
    height: auto;
  }
  
  .emotion-petal {
    cursor: pointer;
    transition: all 0.3s ease;
  }
  
  .emotion-petal:hover {
    filter: brightness(1.1);
  }
  
  .emotion-petal.selected {
    filter: brightness(1.15);
  }
  
  .emotion-label {
    font-size: 11px;
    font-weight: 500;
    pointer-events: none;
    user-select: none;
    fill: #333;
    transition: all 0.3s ease;
  }
  
  .emotion-label.selected {
    font-weight: 700;
    fill: #FF6B00;
  }
  
  .emotion-label.small {
    font-size: 9px;
  }
  
  .emotion-label.tiny {
    font-size: 7px;
  }
  
  .selection-indicator {
    text-align: center;
    color: white;
    margin-top: 1.5rem;
    font-size: 1.1rem;
    padding: 0.75rem;
    background: rgba(255, 255, 255, 0.15);
    border-radius: 10px;
    backdrop-filter: blur(10px);
  }
  
  .selection-indicator strong {
    color: #FFE4B5;
    text-transform: capitalize;
  }
  
  .submit-btn {
    display: block;
    margin: 1.5rem auto 0;
    padding: 0.75rem 2.5rem;
    background: #C87844;
    color: white;
    border: none;
    border-radius: 25px;
    font-size: 1rem;
    cursor: pointer;
    transition: all 0.3s ease;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);
  }
  
  .submit-btn:hover:not(:disabled) {
    background: #B86834;
    transform: translateY(-2px);
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.25);
  }
  
  .submit-btn:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
</style>