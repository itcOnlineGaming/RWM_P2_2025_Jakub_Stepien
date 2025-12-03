<!-- src/routes/emotion/[emotion]/+page.svelte -->
<script lang="ts">
  import { page } from '$app/stores';
  import { goto } from '$app/navigation';
  import { onMount } from 'svelte';
  
  interface EmotionInfo {
    description: string;
    howItHelps: string;
    practices: string[];
  }
  
  interface EmotionInfoMap {
    [key: string]: EmotionInfo;
  }
  
  const emotionInfo: EmotionInfoMap = {
    joy: {
      description: 'A feeling of great pleasure and happiness',
      howItHelps: 'Joy helps with creativity and motivation. It strengthens social bonds, boosts your immune system, and helps you see opportunities and possibilities.',
      practices: ['Practice gratitude daily', 'Engage in activities you love', 'Share positive moments with others']
    },
    trust: {
      description: 'A feeling of confidence and safety with someone or something',
      howItHelps: 'Trust helps build strong relationships and reduces anxiety. It allows you to collaborate effectively and creates a foundation for personal growth.',
      practices: ['Be reliable and keep promises', 'Practice vulnerability', 'Give others the benefit of the doubt']
    },
    fear: {
      description: 'An unpleasant emotion caused by the threat of danger or pain',
      howItHelps: 'Fear keeps you safe by alerting you to danger. It sharpens your focus and helps you prepare for challenges.',
      practices: ['Acknowledge your fears', 'Take small steps to face them', 'Practice breathing exercises']
    },
    surprise: {
      description: 'A feeling caused by something unexpected',
      howItHelps: 'Surprise helps you adapt to change and stay alert. It enhances learning and memory formation.',
      practices: ['Stay curious', 'Try new experiences', 'Practice mindfulness']
    },
    sadness: {
      description: 'A feeling of sorrow or unhappiness',
      howItHelps: 'Sadness helps you process loss and signals to others that you need support. It promotes reflection and deeper understanding.',
      practices: ['Allow yourself to feel', 'Reach out for support', 'Journal your thoughts']
    },
    disgust: {
      description: 'A feeling of strong disapproval or revulsion',
      howItHelps: 'Disgust protects you from harmful substances and situations. It helps maintain personal boundaries and values.',
      practices: ['Honor your boundaries', 'Practice self-care', 'Identify your values']
    },
    anger: {
      description: 'A strong feeling of displeasure or hostility',
      howItHelps: 'Anger signals that something is wrong and gives you energy to make changes. It helps you stand up for yourself and others.',
      practices: ['Express anger constructively', 'Exercise or move your body', 'Identify the underlying need']
    },
    anticipation: {
      description: 'A feeling of excitement about something that is going to happen',
      howItHelps: 'Anticipation motivates planning and goal-setting. It creates hope and helps you prepare for the future.',
      practices: ['Set meaningful goals', 'Visualize positive outcomes', 'Break plans into steps']
    },
    optimism: {
      description: 'A hopeful and confident outlook on the future',
      howItHelps: 'Optimism builds resilience and helps you persevere through challenges. It improves physical health and relationships.',
      practices: ['Focus on solutions', 'Celebrate small wins', 'Surround yourself with positive people']
    },
    love: {
      description: 'An intense feeling of deep affection and care',
      howItHelps: 'Love creates strong bonds and provides emotional security. It improves overall wellbeing and gives life meaning.',
      practices: ['Express appreciation', 'Spend quality time together', 'Practice acts of kindness']
    },
    submission: {
      description: 'A feeling of accepting another\'s authority or will',
      howItHelps: 'Submission in healthy contexts allows cooperation and learning. It helps navigate social hierarchies and build trust.',
      practices: ['Choose your battles wisely', 'Learn from mentors', 'Practice humility']
    },
    awe: {
      description: 'A feeling of reverential respect mixed with wonder',
      howItHelps: 'Awe expands perspective and reduces stress. It promotes curiosity and connection to something greater.',
      practices: ['Spend time in nature', 'Appreciate art and beauty', 'Practice mindful observation']
    },
    disapproval: {
      description: 'A feeling that something is wrong or unacceptable',
      howItHelps: 'Disapproval helps maintain standards and values. It guides moral decision-making and protects against harmful influences.',
      practices: ['Clarify your values', 'Communicate boundaries', 'Choose your influences']
    },
    remorse: {
      description: 'A feeling of regret for wrongdoing',
      howItHelps: 'Remorse promotes accountability and personal growth. It helps repair relationships and prevents future mistakes.',
      practices: ['Make amends when possible', 'Learn from mistakes', 'Practice self-forgiveness']
    },
    contempt: {
      description: 'A feeling that someone or something is worthless',
      howItHelps: 'Contempt can signal violated values, but often needs reframing. It can motivate standing up for principles when channeled constructively.',
      practices: ['Examine underlying hurt', 'Practice empathy', 'Set healthy boundaries']
    },
    aggressiveness: {
      description: 'A feeling of readiness to attack or confront',
      howItHelps: 'Healthy aggressiveness provides assertiveness and drive. It helps you pursue goals and protect what matters.',
      practices: ['Channel energy into action', 'Practice assertive communication', 'Exercise regularly']
    },
    serenity: {
      description: 'A feeling of calm peacefulness',
      howItHelps: 'Serenity reduces stress and improves decision-making. It enhances health and creates space for reflection.',
      practices: ['Meditate regularly', 'Create peaceful routines', 'Spend time in calm environments']
    },
    acceptance: {
      description: 'A feeling of embracing reality as it is',
      howItHelps: 'Acceptance reduces suffering and opens doors to change. It builds resilience and improves relationships.',
      practices: ['Practice mindfulness', 'Let go of control', 'Focus on what you can change']
    },
    apprehension: {
      description: 'A feeling of anxious worry about the future',
      howItHelps: 'Apprehension helps you prepare and stay cautious. It can motivate planning and problem-solving.',
      practices: ['Plan and prepare', 'Challenge worried thoughts', 'Take small brave actions']
    },
    distraction: {
      description: 'A feeling of attention being pulled away',
      howItHelps: 'Distraction can provide breaks from intensity and allow subconscious processing. It signals when something needs attention.',
      practices: ['Notice what distracts you', 'Take intentional breaks', 'Return to the present moment']
    },
    pensiveness: {
      description: 'A feeling of deep, often melancholic thoughtfulness',
      howItHelps: 'Pensiveness allows processing and integration of experiences. It deepens understanding and promotes wisdom.',
      practices: ['Journal regularly', 'Allow quiet time', 'Explore your thoughts']
    },
    boredom: {
      description: 'A feeling of weariness from lack of interest',
      howItHelps: 'Boredom signals need for change or challenge. It can spark creativity and motivation for growth.',
      practices: ['Try something new', 'Reflect on your needs', 'Allow creative exploration']
    },
    annoyance: {
      description: 'A feeling of mild anger or irritation',
      howItHelps: 'Annoyance signals boundary violations or unmet needs. It provides early warning before anger escalates.',
      practices: ['Identify the irritant', 'Communicate needs clearly', 'Practice patience']
    },
    interest: {
      description: 'A feeling of curiosity or concern about something',
      howItHelps: 'Interest drives learning and engagement. It motivates exploration and builds expertise.',
      practices: ['Follow your curiosity', 'Ask questions', 'Explore new topics']
    }
  };
  
  let emotion = '';
  let note = '';
  let emotionData: EmotionInfo;
  
  onMount(() => {
    emotion = $page.params.emotion;
    emotionData = emotionInfo[emotion] || {
      description: 'An important human emotion',
      howItHelps: 'Every emotion serves a purpose in helping us navigate life.',
      practices: ['Notice when you feel this', 'Be curious about it', 'Accept it without judgment']
    };
  });
  
  function handleSubmit() {
    // Load current counts
    const stored = localStorage.getItem('emotionCounts');
    let counts = stored ? JSON.parse(stored) : {};
    
    // Increment count for this emotion
    if (!counts[emotion]) {
      counts[emotion] = { name: emotion, count: 0 };
    }
    counts[emotion].count += 1;
    
    // Save note if provided
    if (note.trim()) {
      const notes = JSON.parse(localStorage.getItem('emotionNotes') || '[]');
      notes.push({
        emotion,
        note: note.trim(),
        timestamp: new Date().toISOString()
      });
      localStorage.setItem('emotionNotes', JSON.stringify(notes));
    }
    
    // Save updated counts
    localStorage.setItem('emotionCounts', JSON.stringify(counts));
    
    // Return to main screen with a flag to reload
    goto('/', { replaceState: true });
  }
  
  function handleBack() {
    goto('/', { replaceState: true });
  }
</script>

<main>
  <div class="container">
    <h1>You selected "{emotion}"</h1>
    
    <div class="info-section">
      <h2>What does "{emotion}" mean?</h2>
      <p class="description">{emotionData?.description || ''}</p>
    </div>
    
    <div class="info-section">
      <h2>How can "{emotion}" help you now?</h2>
      <p class="help-text">{emotionData?.howItHelps || ''}</p>
      
      {#if emotionData?.practices}
        <ul class="practices">
          {#each emotionData.practices as practice}
            <li>{practice}</li>
          {/each}
        </ul>
      {/if}
    </div>
    
    <div class="note-section">
      <label for="note">
        Add a note about why you feel "{emotion}" now (Optional)
      </label>
      <textarea
        id="note"
        bind:value={note}
        placeholder="Enter Note..."
        rows="4"
      ></textarea>
    </div>
    
    <div class="button-group">
      <button class="back-btn" on:click={handleBack}>Back</button>
      <button class="submit-btn" on:click={handleSubmit}>Submit</button>
    </div>
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
    background: rgba(255, 255, 255, 0.95);
    border-radius: 20px;
    padding: 3rem;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  }
  
  h1 {
    color: #333;
    text-align: center;
    font-size: 1.8rem;
    margin-bottom: 2rem;
    font-weight: 500;
  }
  
  .info-section {
    margin-bottom: 2rem;
  }
  
  h2 {
    color: #555;
    font-size: 1.1rem;
    margin-bottom: 0.75rem;
    font-weight: 500;
  }
  
  .description, .help-text {
    color: #666;
    line-height: 1.6;
    font-style: italic;
    margin-bottom: 1rem;
  }
  
  .practices {
    list-style: none;
    padding: 0;
    margin-top: 1rem;
  }
  
  .practices li {
    color: #555;
    padding: 0.5rem 0;
    padding-left: 1.5rem;
    position: relative;
  }
  
  .practices li::before {
    content: '•';
    position: absolute;
    left: 0.5rem;
    color: #E89968;
    font-weight: bold;
  }
  
  .note-section {
    margin-bottom: 2rem;
  }
  
  label {
    display: block;
    color: #555;
    margin-bottom: 0.75rem;
    font-size: 0.95rem;
  }
  
  textarea {
    width: 100%;
    padding: 1rem;
    border: 2px solid #ddd;
    border-radius: 10px;
    font-family: inherit;
    font-size: 0.95rem;
    resize: vertical;
    transition: border-color 0.3s ease;
  }
  
  textarea:focus {
    outline: none;
    border-color: #E89968;
  }
  
  textarea::placeholder {
    color: #999;
  }
  
  .button-group {
    display: flex;
    gap: 1rem;
    justify-content: center;
  }
  
  button {
    padding: 0.75rem 2.5rem;
    border: none;
    border-radius: 25px;
    font-size: 1rem;
    cursor: pointer;
    transition: all 0.3s ease;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);
    font-weight: 500;
  }
  
  .back-btn {
    background: #FFA500;
    color: white;
  }
  
  .back-btn:hover {
    background: #FF8C00;
    transform: translateY(-2px);
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.2);
  }
  
  .submit-btn {
    background: #FF8C00;
    color: white;
  }
  
  .submit-btn:hover {
    background: #FF7700;
    transform: translateY(-2px);
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.2);
  }
</style>