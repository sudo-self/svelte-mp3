
<script>
  import { onMount, afterUpdate } from 'svelte';
  import { writable } from 'svelte/store';
  const messageCount = writable(0);
  let messageText = '';
  let messages = ["love this!", "turn it up", "woah", "ayeeee", "🔥"]; 
  let isDarkMode = true; 
  let isLoading = false;

  async function loadMessages() {
    // Load more messages from the server or local storage if needed
    messageCount.set(messages.length);
  }

  async function addMessage(message) {
   
    messages = [...messages, message];
    messageCount.update(n => n + 1);
  }

  onMount(loadMessages);

  function handleSubmit(event) {
    event.preventDefault();
    if (messageText.trim() !== '') {
      addMessage(messageText);
      messageText = '';
    }
  }

  function toggleDarkMode() {
    isDarkMode = !isDarkMode;
  }

  function getRandomColor() {
    const r = Math.floor(Math.random() * 256);
    const g = Math.floor(Math.random() * 256);
    const b = Math.floor(Math.random() * 256);
    return `rgb(${r},${g},${b})`;
  }

  function handleScroll() {
    const { scrollTop, clientHeight, scrollHeight } = document.documentElement;
    if (scrollTop + clientHeight >= scrollHeight - 5 && !isLoading) {
      isLoading = true;
      setTimeout(() => {
        isLoading = false;
      }, 1000);
    }
  }

  afterUpdate(() => {
    window.addEventListener('scroll', handleScroll);
    return () => {
      window.removeEventListener('scroll', handleScroll);
    };
  });

  const playlist = [
    {		
			artist: 'Radio Mashup',
      name: 'Sudo-Self',
      audio: `https://pub-090188261ed842a9ac0918908eb278e5.r2.dev/01%20codec%20ninja.mp3`
    },
  ];

  let playingState = 'paused';
  let songPlayingIndex = 0;
  let song = null;

  function togglePlaying() {
    playingState === 'paused' ? play() : pause();
  }

  function loadSong() {
    song = new Audio(playlist[songPlayingIndex].audio);
    song.volume = 0.2;
    song.play();
  }

  function play() {
    if (playingState === 'playing') {
      pause();
    }
    playingState = 'playing';
    loadSong();
  }

  function playSelectedSong(event) {
    const songIndex = +event.target.dataset.index;
    if (songIndex === songPlayingIndex) {
      songPlayingIndex = null;
      return pause();
    }
    songPlayingIndex = songIndex;
    play();
  }

  function pause() {
    playingState = 'paused';
    song.pause();
  }

  function previous() {
    if (songPlayingIndex <= 0) return;
    songPlayingIndex -= 1;
    play();
  }

  function next() {
    if (songPlayingIndex >= playlist.length - 1) return;
    songPlayingIndex += 1;
    play();
  }
</script>
<div class="{`centered-container ${isDarkMode ? 'dark-mode' : ''}`}">
<div class="player">
  <div class="playlist">
    {#each playlist as song, index}
      <div class:playing={index === songPlayingIndex} class="song">
        <img src="/svelte.svg" alt="Book Icon" class="book-icon" />
        <span>{song.name} - {song.artist}</span>
      </div>
    {/each}
  </div>

  <div class="controls">
  
    <button on:click={togglePlaying}>
      {playingState === 'paused' ? '▶️' : '⏯️'}
    </button>

  </div>
</div>


  <h2>@iLostMyipad</h2>
  <div class="message-count-container">
    <img src="/vite.svg" alt="Book Icon" class="book-icon" />
    <span>{$messageCount}</span>
  </div>
  <button on:click={toggleDarkMode} style="margin-bottom: 10px;">Color Mode</button>
  <form on:submit={handleSubmit}>
    <input type="text" bind:value={messageText} placeholder="drop a message..." required>
    <br>
    <button type="submit">POST</button>
  </form>
  <ul class="message-list">
    {#each messages as message}
      <li class="bubble" style="background-color: {getRandomColor()}">{message}</li>
    {/each}
  </ul>
</div>

<style>
  button {
    margin: 0;
    padding: 0;
    font-size: 1.4rem;
    font-weight: 700;
    background: none;
    border: none;
    cursor: pointer;
  }

  .player {
    color: hsl(220 20% 80%);
    border-radius: 8px;
  }

  .playlist {
    padding: 1rem;
  }

  .song {
    display: flex;
    align-items: center;
    padding: 1rem;
  }

  .song button {
    padding: 0 0.4rem;
  }

  .controls {
    display: flex;
    justify-content: center;
    gap: 24px;
    padding: 1rem 0;
    border-top: 1px solid hsl(220 20% 28%);
  }

  .controls button {
    font-size: 2rem;
  }

  .playing {
    color: hsl(220 20% 98%);
    border-radius: 8px;
  }

  .dark-mode {
    background-color: #333;
    color: white;
  }

  .centered-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-height: 100vh;
  }

  .message-list {
    list-style-type: none;
    padding-inline-start: 0;
  }

  .bubble {
    margin-bottom: 10px;
    padding: 10px;
    border-radius: 5px;
    border: 1px solid #ccc;
  }

  .message-count-container {
    display: flex;
    align-items: center;
    margin-bottom: 10px;
  }

  .book-icon {
    width: 24px;
    height: 24px;
    margin-right: 8px;
  }
</style>


