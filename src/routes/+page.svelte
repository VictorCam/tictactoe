<script>
    let board = Array(9).fill(null);
    let currentPlayer = "X";
    let winner = null;
    let turnIndicator = "X's Turn";

    const handleClick = async (index) => {
        // prevent the player from selecting a spot that is taken or isn't their turn or the game is over
        if (board[index] || winner || currentPlayer !== "X") return;

        // update the board
        board[index] = currentPlayer;
        if (calculateWinner()) {
            winner = currentPlayer;
            turnIndicator = "Game Over";
        } else if (board.every((cell) => cell !== null)) {
            winner = "Draw";
            turnIndicator = "Game Over";
        } else {
            currentPlayer = currentPlayer === "X" ? "O" : "X";
            turnIndicator = `O's Turn`;
            if (currentPlayer === "O") {
                setTimeout(makeComputerMove, 1000);
                await new Promise((resolve) => setTimeout(resolve, 1000));
                turnIndicator = `X's Turn`;
            }
        }
    };

    const calculateWinner = () => {
        const lines = [
            [0, 1, 2],
            [3, 4, 5],
            [6, 7, 8],
            [0, 3, 6],
            [1, 4, 7],
            [2, 5, 8],
            [0, 4, 8],
            [2, 4, 6],
        ];

        // check if there is a winner
        for (let i = 0; i < lines.length; i++) {
            const [a, b, c] = lines[i];
            if (board[a] && board[a] === board[b] && board[a] === board[c]) {
                return true;
            }
        }

        return false;
    };

    const makeComputerMove = () => {
        currentPlayer = "O";

        // find available spots for CPU
        const availableSpots = board.reduce((acc, value, index) => {
            if (value === null) {
                acc.push(index);
            }
            return acc;
        }, []);

        // Check if there are available spots
        if (availableSpots.length > 0) {
            const randomIndex = Math.floor(
                Math.random() * availableSpots.length,
            );
            const chosenIndex = availableSpots[randomIndex];

            board[chosenIndex] = currentPlayer;

            if (calculateWinner()) {
                winner = currentPlayer;
            } else if (board.every((cell) => cell !== null)) {
                winner = "Draw";
            }

            currentPlayer = "X";
        } else {
            currentPlayer = "X";
        }
    };

    const resetGame = () => {
        board = Array(9).fill(null);
        currentPlayer = "X";
        winner = null;
        turnIndicator = "X's Turn";
    };
</script>

<main>
    <div class="parent">
        <div aria-label="tic-tac-toe" class="svg-color tictactoe-title" />
    </div>

    <div class="stack stack-center">
        <div class="board">
            {#each board as value, index (index)}
                <button class="cell" on:click={() => handleClick(index)}>
                    {#if value !== null}
                        {value}
                    {/if}
                </button>
            {/each}
        </div>
        <div class="svg-color tictactoe-board"></div>
    </div>

    <button class="reset" on:click={resetGame}>Reset Game</button>

    {#if winner === "Draw"}
        <p class="winner">It's a Draw!</p>
    {:else if winner === null}
        <p class="winner">{turnIndicator}</p>
    {:else}
        <p class="winner">Winner: {winner}</p>
    {/if}
</main>

<style>
    @import "../icons.css";
    @import "../utils.css";
    @import "../animations.css";

    main {
        overflow: hidden;
    }

    .tictactoe-board {
        padding: 170px;
        position: relative;
        bottom: -15px;
        user-select: none;
        pointer-events: none;
    }

    .parent {
        user-select: none;
        pointer-events: none;
        display: grid;
        place-items: center;
        margin-top: 50px;
    }

    .tictactoe-title {
        padding: 100px;
        user-select: none;
        overflow: hidden;
        position: absolute;
        font-family: "Vanilla Extract", Arial, Helvetica, sans-serif;
    }

    main {
        font-family: Arial, Helvetica, sans-serif;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        height: 100vh;
        background-color: #111;
        color: #eee;
    }

    .board {
        cursor: pointer;
        display: grid;
        grid-template-columns: repeat(3, 100px);
        grid-template-rows: repeat(3, 100px);
        gap: 5px;
        margin-top: 20px;
    }

    .cell {
        cursor: pointer;
        background: none;
        border: none;
        width: 100px;
        height: 100px;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 70px;
        font-weight: bold;
        /* border: 1px solid #ccc; */
        color: #ccc;
    }

    .reset {
        font-size: 1.5em;
        color: #ccc;
        background: rgb(150, 43, 43);
        border: none;
        padding: 5px;
        border-radius: 5px;
        margin-top: 20px;
        cursor: pointer;
    }

    .reset:hover {
        background: rgb(117, 32, 32);
    }

    .reset:active {
        background: rgb(85, 22, 22);
    }

    .winner {
        font-size: 1.5em;
        padding: 10px;
        text-align: center;
    }
</style>
