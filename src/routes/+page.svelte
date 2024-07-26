<script lang="ts">
  import { invoke } from "@tauri-apps/api/core";
  import { Trash2 } from 'lucide-svelte';
  import '../app.scss';

  let page = $state("home");
  let rounds = $state(10);
  let playerNameInput = $state("");
  // name, score, banking
  let players: Array<[string, number, boolean]> = $state([]);
  let winner: [string, number, boolean] = $state(["NO ONE", 0, false]);
  let currentRound = $state(1);
  let currentRoll = $state(1);
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

  function roundEnd() {
    if (currentRound < rounds) {
      currentScore = 0;
      currentRoll = 1;
      currentRound += 1;

      for (let i=0; i<players.length; i++) {
        players[i][2] = false;
      }
    } else {
      page = "end";
      
      for (let i=0; i<players.length; i++) {
        if (players[i][1] > winner[1]) {
          winner = players[i];
        }
      }
    }
  }

  function resetGame() {
    currentScore = 0;
    currentRound = 1;
    currentRoll = 1;
    winner = ["NO ONE", 0, false];
    page = "home";

    for (let i=0; i<players.length; i++) {
      players[i][1] = 0;
      players[i][2] = false;
    }
  }

  function bank(playerIndex: number) {
    if (currentScore > 0) {
      players[playerIndex][2] = true;
      players[playerIndex][1] += currentScore;

      let allPlayersBanked = true;
      for (let i=0; i<players.length; i++) {
        if (players[i][2] === false) {
          allPlayersBanked = false;
        }
      }
      if (allPlayersBanked === true) {
        roundEnd();
      } 
    }
  }

  function undoBank(playerIndex: number) {
    players[playerIndex][2] = false;
    players[playerIndex][1] -= currentScore;
  }

  function rollPressed(button: number) {
    if (button === 7 && currentRoll <= 3) {
      currentScore += 70;
      currentRoll += 1;
    }
    else if (button === 0 && currentRoll > 3) {
      currentScore *= 2;
      currentRoll += 1;
    } else if (button === 7) {
      roundEnd();
    } else if (button !== 0) {
      currentRoll += 1;
      currentScore += button;
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
        <button type="button" class="remove-player" onclick={() => removePlayer(playerData[0])}>
          <Trash2 size="16" strokeWidth="2"/>
        </button>
      </div>
      {/each}
    </div>
  
    <button type="button" onclick={onPlay}>Play!</button>

  {:else if page === "game"}
    <p>Round: {currentRound}/{rounds}</p>
    <p>Current Pot: {currentScore}</p>
    {#each players as playerData, i}
    <div class="player">
      <p>{playerData[0]}</p>
      {#if playerData[2] === false}
        <button class="bank-button" onclick={() => bank(i)}>Bank!</button>
      {:else}
        <button class="bank-button" onclick={() => undoBank(i)}>Undo Bank</button>
      {/if}
      <p>{playerData[1]}</p>
    </div>
    {/each}

    <div class="roll-menu">
      {#each [2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12] as num}
        <button class="roll-item" onclick={() => rollPressed(num)}>{num}</button>
      {/each}
      <button class="roll-item" onclick={() => rollPressed(0)}>Doubles</button>
    </div>
  {:else if page === "end"}
    <p>{winner[0]} won the game with {winner[1]} points!</p>
    <button onclick={resetGame}>Play Again!</button>
  {/if}
</div>