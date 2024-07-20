<script lang="ts">
  import { invoke } from "@tauri-apps/api/core";
  import { Trash2 } from 'lucide-svelte';
  import '../app.scss';

  let page = $state("home");
  let rounds = $state(10);
  let playerNameInput = $state("");
  // name, score
  let players: Array<[string, number]> = $state([]);
  let currentRound = $state(0);
  let currentTurn = $state(0);

  function onPlay() {
    if (players.length >= 2) {
      page = "game";
    }
  }

  function removePlayer(playerName: string) {
    players = players.filter(item => item[0] != playerName);
  }

  function addPlayer(playerName: string) {
    if (playerName.length > 0 && !players.includes([playerName, 0])) {
      players.push([playerName, 0]);
      playerNameInput = "";
    }
  }
</script>

<div class="container">
  {#if page === "home"}
    <h1>BANK</h1>
    <input type="range" bind:value={rounds} min="5" max="30"/>
    <p>{rounds} rounds</p>

    <form class="row" onsubmit={() => addPlayer(playerNameInput)}>
      <input type="text" placeholder="Player Name..." bind:value={playerNameInput}/>
      <button type="submit">Add</button>
    </form>
    <div class="players">
      {#each players as playerData}
      <div class="player">
        <p>{playerData[0]}</p>
        <button type="button" onclick={() => removePlayer(playerData[0])}>
          <Trash2 size="16" strokeWidth="2"/>
        </button>
      </div>
      {/each}
    </div>
  
    <button type="button" onclick={onPlay}>Play!</button>

  {:else if page === "game"}
    {#each players as playerData}
    <div class="player">
      <p>{playerData[0]}</p>
      <p>{playerData[1]}</p>
    </div>
    {/each}
  {/if}
</div>