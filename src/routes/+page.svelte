<!-- src/routes/+page.svelte -->
<script lang="ts">
  import { onMount } from 'svelte';
  import { goto } from '$app/navigation';
  import EmotionWheel from '$lib/components/EmotionWheel.svelte';
  
  interface EmotionData {
    name: string;
    count: number;
  }
  
  interface EmotionMap {
    [key: string]: EmotionData;
  }
  
  // Plutchik's emotions
  const allEmotions = [
    'joy', 'trust', 'fear', 'surprise',
    'sadness', 'disgust', 'anger', 'anticipation',
    'optimism', 'love', 'submission', 'awe',
    'disapproval', 'remorse', 'contempt', 'aggressiveness',
    'serenity', 'acceptance', 'apprehension', 'distraction',
    'pensiveness', 'boredom', 'annoyance', 'interest'
  ];
  
  let emotionCounts: EmotionMap = {};
  let selectedEmotion: string | null = null;
  let emotionNotes: { [key: string]: Array<{ note: string; timestamp: string }> } = {};
  let componentKey = 0; // Force component re-render
  
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
      const newCounts: EmotionMap = {};
      allEmotions.forEach(emotion => {
        newCounts[emotion] = { name: emotion, count: 0 };
      });
      emotionCounts = newCounts;
    }
    
    // Load notes
    const notesStored = localStorage.getItem('emotionNotes');
    if (notesStored) {
      const allNotes = JSON.parse(notesStored);
      // Group notes by emotion
      const grouped: { [key: string]: Array<{ note: string; timestamp: string }> } = {};
      allNotes.forEach((entry: any) => {
        if (!grouped[entry.emotion]) {
          grouped[entry.emotion] = [];
        }
        grouped[entry.emotion].push({ note: entry.note, timestamp: entry.timestamp });
      });
      emotionNotes = grouped;
    }
    
    console.log('Main page - emotionCounts:', emotionCounts);
    
    // Force reactivity by creating new object references
    emotionCounts = { ...emotionCounts };
    emotionNotes = { ...emotionNotes };
    componentKey++; // Increment to force re-render
  }
  
  function handleEmotionSelect(event: CustomEvent<string>) {
    selectedEmotion = event.detail;
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
        selectedEmotion = null;
      }
    };
    
    // Also reload on focus and when navigating back
    const handleFocus = () => {
      loadEmotionCounts();
    };
    
    // Listen for popstate (browser back button)
    const handlePopState = () => {
      loadEmotionCounts();
      selectedEmotion = null;
    };
    
    document.addEventListener('visibilitychange', handleVisibilityChange);
    window.addEventListener('focus', handleFocus);
    window.addEventListener('popstate', handlePopState);
    
    return () => {
      document.removeEventListener('visibilitychange', handleVisibilityChange);
      window.removeEventListener('focus', handleFocus);
      window.removeEventListener('popstate', handlePopState);
    };
  });
</script>

<main>
  <div class="container">
    <h1>What emotion are you feeling right now?</h1>
    
    {#key componentKey}
      <EmotionWheel 
        {emotionCounts}
        {selectedEmotion}
        {emotionNotes}
        on:select={handleEmotionSelect}
      />
    {/key}
    
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