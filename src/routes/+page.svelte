<script lang="ts">
  import { invoke } from "@tauri-apps/api/core";
  import { Trash2 } from 'lucide-svelte';
  import '../app.scss';

  let page = $state("home");
  let rounds = $state(10);
  let playerNameInput = $state("");
  // name, score, banking
  let players: Array<[string, number, boolean]> = $state([]);
  let currentRound = $state(0);
  let currentTurn = $state(0);
  let currentScore = $state(0);

  function onPlay() {
    if (players.length >= 2) {
      page = "game";
    }
  }

  function removePlayer(playerName: string) {
    players = players.filter(item => item[0] != playerName);
  }

  function addPlayer(playerName: string) {
    if (playerName.length > 0 && !players.includes([playerName, 0, false])) {
      players.push([playerName, 0, false]);
      playerNameInput = "";
    }
  }

  function bank(playerIndex: number) {
    players[playerIndex][2] = true;
  }

  function undoBank(playerIndex: number) {
    players[playerIndex][2] = false;
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
    {#each players as playerData, i}
    <div class="player">
      <p>{playerData[0]}</p>
      {#if playerData[2] === false}
        <button onclick={() => bank(i)}>Bank!</button>
      {:else}
        <button onclick={() => undoBank(i)}>Undo Bank</button>
      {/if}
      <p>{playerData[1]}</p>
    </div>
    {/each}

    <div class="roll-menu">
      {#each [2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12] as num}
        <button class="roll-item">{num}</button>
      {/each}
      <button class="roll-item">Doubles</button>
    </div>
  {/if}
</div>