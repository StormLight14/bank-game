<script lang="ts">
  import { invoke } from "@tauri-apps/api/core";
  import { Trash2 } from 'lucide-svelte';
  import '../app.scss';

  let page = $state("home");
  let rounds = $state(10);
  let playerName = $state("");
  let players: String[] = $state(["john"]);

  function onPlay() {
    if (players.length >= 2) {
      page = "players";
    }
  }

  function removePlayer(playerName: String) {
    console.log("test");
    if (players.includes(playerName)) {
      players = players.filter(item => item != playerName);
    }
  }

  function addPlayer(playerName: String) {
    console.log("add player")
    if (playerName.length > 0 && !players.includes(playerName)) {
      players.push(playerName);
      playerName = "";
    }
  }
</script>

{#if page === "home"}
  <div class="container">
    <h1>BANK</h1>
    <input type="range" bind:value={rounds} min="5" max="30"/>
    <p>{rounds} rounds</p>

    <form class="row" onsubmit={() => addPlayer(playerName)}>
      <input type="text" placeholder="Player Name..." bind:value={playerName}/>
      <button type="submit">Add</button>
    </form>
    <div class="players">
      {#each players as player}
      <div class="player">
        <p>{player}</p>
        <button type="button" onclick={() => removePlayer(player)}>
          <Trash2 size="16" strokeWidth="2"/>
        </button>
      </div>
      {/each}
    </div>
  
    <button type="button" onclick={onPlay}>Play!</button>
  </div>

{:else if page === "game"}
  
{/if}