<script>
    const board = createBoard();
    let notDead = true
    let snake = [[10, 10]]
    let moveYX = [0, 1]
    let score = 0
    let maxScore = 0;
    updateSnake()
    /**
     * Creates a N x N board
     * @returns Array<Array<Number>>
    */
    function createBoard(){
        const newBoard = []
        for(let i = 0; i < 20; i++){
            const row = []
            for(let j = 0; j < 20; j++){
                row.push(0)
            }
            newBoard.push(row)
        }
        generateFood(newBoard)
        generateFood(newBoard)
        generateFood(newBoard)
        return newBoard
    }

    async function start(){
        notDead = true
        let speed = 500
        let willDie = false
        while(notDead){
            await delay(speed)
            if(willDie){
                await delay(speed)
            }
            if(canMove()){
                moveSnake()
                speed = Math.max(125, 500 - (50*score))
                willDie = false
            }
            else{
                if(willDie) break
                willDie = true
            }
        }
        maxScore = Math.max(score, maxScore)
        const answer = confirm(`You died\n Current Score: ${score} \n Best Score: ${maxScore}`)
        notDead = false
        reset()
    }

    function canMove(){
        return snake[0][0] + moveYX[0] < board.length && snake[0][1] + moveYX[1] < board.length && 
                snake[0][0] + moveYX[0] >= 0 && snake[0][1] + moveYX[1] >= 0 &&
                board[snake[0][0] + moveYX[0]][snake[0][1] + moveYX[1]] !== 1
    }


    function moveSnake(){
        const newHead = [snake[0][0] + moveYX[0], snake[0][1] + moveYX[1]]
        if(board[newHead[0]][newHead[1]] !== 2){
            const last = snake[snake.length-1]
            board[last[0]][last[1]] = 0
            snake.pop()
            snake = [newHead, ...snake]

            updateSnake()
        }else{
            snake = [newHead, ...snake]
            updateSnake()
            generateFood(board)
            score++
        }

    }

    function updateSnake(){
        for(const link of snake){
            board[link[0]][link[1]] = 1
        }
    }

    function reset(){
        if(notDead) return
        snake = [[10, 10]]
        for(const i in board){
            for(const j in board[0]){
                board[i][j] = 0
            }
        }
        score = 0
        updateSnake()
        generateFood(board)
        generateFood(board)
        generateFood(board)
    }

    /**
     * 
     * @param {number[][]} board
     */
    function generateFood(board){
        let y = Math.floor(Math.random() * 20)
        let x = Math.floor(Math.random() * 20)
        while(board[y][x] !== 0){
            y = Math.floor(Math.random() * 20)
            x = Math.floor(Math.random() * 20)
        }
        board[y][x] = 2
    }

    /**
     * Creates promise that resolves after n miliseconds
     * @param {Number} n
    */
   async function delay(n){
    return new Promise(resolve => {
        setTimeout( () => {
            resolve('')
        }, n)
    })
   }

   /**
    * 
    * @param {KeyboardEvent} e
    */
   function onKeyDown(e){
    switch (e.key){
        case "w":
        case "ArrowUp":
            moveYX = [-1, 0]
            break
        case "s":
        case "ArrowDown":
            moveYX = [1, 0]
            break
        case "a":    
        case "ArrowLeft":
            moveYX = [0, -1]
            break
        case "d":
        case "ArrowRight":
            moveYX = [0, 1]
            break
    }

   }
</script>

<div style="text-align: center;">

    <button on:click={() => {
        start()
    }}>Start</button>
    <button on:click={reset}>Reset</button>  
    
    <div style="display: inline-block; font-size: 1.25rem; background-color : white; padding: 1rem; border-radius: 5rem">
        Score: {score}
    </div>
    
    <div class="board">
        {#each board as row, i}
            {#each row as col, j}
                {#if col == 0}
                    {#if i % 2 == 0}
                        {#if j % 2 == 0}
                            <div class="board-cell bgdark"></div>
                        {:else}
                            <div class="board-cell bglight"></div>
                        {/if}
                    {:else}
                        {#if j % 2 == 1}
                            <div class="board-cell bgdark"></div>
                        {:else}
                            <div class="board-cell bglight"></div>
                        {/if}
                    {/if}
                {:else if col == 1}
                    <div class="board-cell snake"></div>
                {:else}
                    <div class="board-cell food"></div>
                {/if}
            {/each}
        {/each}
    </div>
</div>

<style>
    button{
        all: unset;
        border-radius: 2rem;
        padding: .4rem;
        font-size: 1.25rem;
        background-color: white;

    }
    .board{
        display: flex;
        flex-wrap: wrap;
        width: min(80vh, 80vw);
        height: min(80vh, 80vw);
        
    }
    .board-cell{
        width: calc(5% - 4px);
        height: calc(5% - 4px);
        border: 2px solid black;
        margin: auto;
        /* border-radius: 1rem; */
    }
    .bglight{
        background-color: lightgreen;
    }
    .bgdark{
        background-color: green;
    }
    .food{
        background-color: gold;
    }
    .snake{
        background-color: blue;
        border-radius: .5rem;
    }
</style>

<svelte:window on:keydown|preventDefault={onKeyDown} />