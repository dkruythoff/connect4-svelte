<script>
  import { play } from "connect4-core";

  export let rows = 6;
  export let cols = 7;

  let gameState = null;
  let touchEnabled = false;
  let highlightedColumn = null;

  startGame();

  function startGame() {
    gameState = play({ cols, rows });
  }

  function handleColumnTouchend(columnIndex) {
    touchEnabled = true;

    if (!columnIsPlayable(columnIndex)) {
      return;
    }

    if (highlightedColumn !== columnIndex) {
      highlightedColumn = columnIndex;
    } else {
      highlightedColumn = null;
      makeMove(columnIndex);
    }
  }

  function handleColumnClick(columnIndex) {
    if (!columnIsPlayable(columnIndex)) {
      return;
    }

    if (touchEnabled) {
      touchEnabled = false;
    } else {
      makeMove(columnIndex);
    }
  }

  function makeMove(columnIndex) {
    gameState = play(gameState, columnIndex);
  }

  function columnIsPlayable(columnIndex) {
    return !(gameState.gameover || !gameState.columnsPlayable[columnIndex]);
  }
</script>

<div class:game={true} class:game--over={gameState.gameover}>
  <div
    class:board={true}
    class:board--playable={!gameState.gameover}
    class:board--turn-1={gameState.turn === 1}
    class:board--turn-2={gameState.turn === 2}>
    {#each gameState.board as column, columnIndex}
      <div
        class:column={true}
        class:column--full={false}
        class:column--highlighted={highlightedColumn === columnIndex}
        on:touchend={() => handleColumnTouchend(columnIndex)}
        on:click={() => handleColumnClick(columnIndex)}>
        {#each column as { value, winner }, cellIndex}
          <div class:cell={true} class:cell--winner={winner}>
            <span
              class:piece={true}
              class:piece--1={value === 1}
              class:piece--2={value === 2}>
              &nbsp;
            </span>
          </div>
        {/each}
        <div class:cell={true} class:cell--preview={true}>
          <span
            class:piece={true}
            class:piece--1={gameState.turn === 1}
            class:piece--2={gameState.turn === 2}>
            &nbsp;
          </span>
        </div>
      </div>
    {/each}
  </div>
  {#if gameState.gameover}
    <div class:won={true}>
      <p>{gameState.turn === 1 ? 'Red' : 'Yellow'} wins!</p>
      <button class:startgame={true} on:click={startGame}>Play again</button>
    </div>
  {/if}
</div>
