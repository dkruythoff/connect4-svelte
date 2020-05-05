<script>
  import { Connect4 } from "../lib/Connect4";

  export let rows = 6;
  export let cols = 7;

  let game = null;
  let touchEnabled = false;
  let highlightedColumn = null;
  let board = null;
  let turn = 0;
  let gameover = false;

  startGame();

  function startGame() {
    game = new Connect4(rows, cols);
    updateObservables();
  }

  function handleColumnTouchend(columnIndex) {
    touchEnabled = true;

    if (!!game.winner || game.freeIndexPerColumn[columnIndex] === -1) {
      return;
    }

    if (highlightedColumn !== columnIndex) {
      highlightedColumn = columnIndex;
    } else {
      highlightedColumn = null;
      console.log("making move on touchend");
      makeMove(columnIndex);
    }
  }

  function handleColumnClick(columnIndex) {
    if (!!game.winner || game.freeIndexPerColumn[columnIndex] === -1) {
      return;
    }

    if (touchEnabled) {
      touchEnabled = false;
    } else {
      console.log("making move on click");
      makeMove(columnIndex);
    }
  }

  function makeMove(columnIndex) {
    game.makeMove(columnIndex);
    updateObservables();
  }

  function updateObservables() {
    turn = game.turn;
    board = game.board;
    gameover = game.winner !== false;
  }
</script>

<div class:game={true} class:game--over={gameover}>
  <div
    class:board={true}
    class:board--playable={true}
    class:board--turn-1={turn === 1}
    class:board--turn-2={turn === 2}>
    {#each board as column, columnIndex}
      <div
        class:column={true}
        class:column--full={false}
        class:column--highlighted={highlightedColumn === columnIndex}
        on:touchend={() => handleColumnTouchend(columnIndex)}
        on:click={() => handleColumnClick(columnIndex)}>
        {#each column as cell, cellIndex}
          <div class:cell={true} class:cell--winner={false}>
            <span
              class:piece={true}
              class:piece--1={cell === 1}
              class:piece--2={cell === 2}>
              &nbsp;
            </span>
          </div>
        {/each}
        <div class:cell={true} class:cell--preview={true}>
          <span
            class:piece={true}
            class:piece--1={turn === 1}
            class:piece--2={turn === 2}>
            &nbsp;
          </span>
        </div>
      </div>
    {/each}
  </div>
  {#if gameover}
    <div class:won={true}>
      <p>{turn === 1 ? 'Red' : 'Yellow'} wins!</p>
      <button class:startgame={true} on:click={startGame}>Play again</button>
    </div>
  {/if}
</div>
