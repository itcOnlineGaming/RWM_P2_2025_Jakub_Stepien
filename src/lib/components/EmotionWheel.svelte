<!-- src/lib/components/EmotionWheel.svelte -->
<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  
  export let emotionCounts: { [key: string]: { name: string; count: number } } = {};
  export let selectedEmotion: string | null = null;
  export let emotionNotes: { [key: string]: Array<{ note: string; timestamp: string }> } = {};
  
  const dispatch = createEventDispatcher();
  
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
  
  let hoveredEmotion: string | null = null;
  
  // Make colors reactive to prop changes
  $: {
    // This will re-run whenever emotionCounts or selectedEmotion changes
    if (emotionCounts) {
      console.log('Component: emotionCounts updated', emotionCounts);
    }
  }
  
  function getEmotionColor(emotion: string): string {
    const count = emotionCounts[emotion]?.count || 0;
    const isSelected = selectedEmotion === emotion;
    
    if (count > 0) {
      console.log(`EmotionWheel - ${emotion}: count=${count}, isSelected=${isSelected}`);
    }

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
    dispatch('select', emotion);
  }
  
  function showNotes(emotion: string) {
    const count = emotionCounts[emotion]?.count || 0;
    if (count > 0) {
      hoveredEmotion = emotion;
    }
  }
  
  function hideNotes() {
    hoveredEmotion = null;
  }
</script>

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
        on:mouseenter={() => showNotes(emotion)}
        on:mouseleave={hideNotes}
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
        on:mouseenter={() => showNotes(emotion)}
        on:mouseleave={hideNotes}
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
        on:mouseenter={() => showNotes(emotion)}
        on:mouseleave={hideNotes}
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
  
  <!-- Notes Popup -->
  {#if hoveredEmotion && emotionNotes[hoveredEmotion] && emotionNotes[hoveredEmotion].length > 0}
    <div class="notes-popup">
      <div class="notes-header">
        <strong>{hoveredEmotion}</strong>
        <span class="notes-count">({emotionCounts[hoveredEmotion]?.count || 0} times)</span>
      </div>
      <div class="notes-list">
        {#each emotionNotes[hoveredEmotion].slice(-3) as noteEntry}
          <div class="note-entry">
            <p class="note-text">{noteEntry.note}</p>
            <span class="note-date">{new Date(noteEntry.timestamp).toLocaleDateString()}</span>
          </div>
        {/each}
      </div>
    </div>
  {/if}
</div>

<style>
  .wheel-container {
    background: white;
    border-radius: 50%;
    padding: 1rem;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
    position: relative;
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
  
  .notes-popup {
    position: absolute;
    top: 50%;
    right: -280px;
    transform: translateY(-50%);
    background: white;
    border-radius: 15px;
    padding: 1.25rem;
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
    width: 260px;
    z-index: 100;
    animation: slideIn 0.2s ease;
  }
  
  @keyframes slideIn {
    from {
      opacity: 0;
      transform: translateY(-50%) translateX(-10px);
    }
    to {
      opacity: 1;
      transform: translateY(-50%) translateX(0);
    }
  }
  
  .notes-header {
    margin-bottom: 1rem;
    padding-bottom: 0.75rem;
    border-bottom: 2px solid #E89968;
  }
  
  .notes-header strong {
    color: #E89968;
    font-size: 1.1rem;
    text-transform: capitalize;
  }
  
  .notes-count {
    color: #999;
    font-size: 0.85rem;
    margin-left: 0.5rem;
  }
  
  .notes-list {
    max-height: 200px;
    overflow-y: auto;
  }
  
  .note-entry {
    margin-bottom: 1rem;
    padding-bottom: 1rem;
    border-border: 1px solid #eee;
  }
  
  .note-entry:last-child {
    margin-bottom: 0;
    padding-bottom: 0;
    border-bottom: none;
  }
  
  .note-text {
    color: #555;
    font-size: 0.9rem;
    line-height: 1.4;
    margin: 0 0 0.5rem 0;
  }
  
  .note-date {
    color: #999;
    font-size: 0.75rem;
    font-style: italic;
  }
  
  @media (max-width: 768px) {
    .notes-popup {
      right: auto;
      left: 50%;
      top: auto;
      bottom: -240px;
      transform: translateX(-50%);
    }
    
    @keyframes slideIn {
      from {
        opacity: 0;
        transform: translateX(-50%) translateY(-10px);
      }
      to {
        opacity: 1;
        transform: translateX(-50%) translateY(0);
      }
    }
  }
</style>