# Game Over and Restart

1. ~~Implement the overall grid square~~
1. ~~Implement the game board~~
1. ~~Implement the "next block" area~~
1. ~~Implement the score board~~
1. ~~Arrange the layout of the game~~
1. ~~Implement the controls~~
1. ~~Implement the message popup~~
1. ~~Implement the actions and reducers~~
1. ~~Do some code organizing and cleanup~~
1. ~~Implement state and shapes~~
1. ~~Connect each component up to state and reducers~~
1. ~~Implement block rotation~~
1. ~~Implement moving blocks~~
1. ~~Building a timer system~~
1. **Implementing Game Over and Restart**
  1. **Handle the behavior of Controls in relation to the game over state**
  1. **Handle the game over the state in the `addBlockToGrid` function to know if the blocks are off the screen**
  1. **Modify reducers to handle the restart action**

If the blocks stack to the top of the screen, it's game over. We need to make sure we can handle this state.

Let's take a closer look at `state`:

- `grid` : An array describing the game board. This contains all of the squares that have been placed. It does not contain the current block that you are controlling.
- `shape` : The index of the current shape that you are controlling. The index identifies the array of shapes for each rotation in the shape array.
- `rotation` : The index of the rotation for the current block.
- `x` : horizontal position of the shape you are controlling.
- `y` : vertical position of the shape you are controlling.
- `nextShape` : index of the next shape that will appear after
the current shape is placed.
- **`isRunning`** : when true, the game is running, false when the
The game is paused.
- **`gameOver`** : true when the game is over.
- `speed` : the number of milliseconds before a block is moved down.
- `score` : your score

`isRunning` and `gameOver` are the two important properties for this discussion, let's go over the basic rules of these properties:

- `isRunning` is set by the Play/Resume button. When `isRunning` is false, the game is paused but not over.
- On the other hand, when `gameOver` is true the game is over.
- The controls should not issue actions when `isRunning` is
false or `gameOver` is true.
- The `moveDown` action that forces the game along should also
not happen when `isRunning` is false or `gameOver` is true.
- `gameOver` is set to false by default. It gets set to true
when the game is over.
- A game is over when a block is added to the grid and part of that block ends up off the top edge of the grid.

# Handle gameOver in the controls component

Here you need to disable the controls when the `gameOver` property on `state` is false.

Use the `useSelector` hook in `/src/components/Controls.js`:

Import useSelector and then get the isRunning and gameOver values from the state. 

**Challenge**

Get the `gameOver` property from the state. 

Check your work against the solution below...

-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-


 
```js
export default function Controls() {
  const dispatch = useDispatch()
  const { isRunning, speed, gameOver } = useSelector(state => state)
  ...
```

**Challenge**

The `onClick` handler for each function should check
`isRunning` and `gameOver`. If `isRunning` is `false`
or `gameOver` is `true` the buttons should not send actions.

Update all of the `button` elements in the `return` block of `/src/components/controls.js` to check `isRunning` and `gameOver` before dispatching their actions.

Check your work against the solution below...

-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-

Here's the "Move Left" button: 

```JS
<button 
  disabled={!isRunning || gameOver}
  className="control-button" 
  onClick={(e) => {
    dispatch(moveLeft())
  }
}>Left</button>
```

**Challenge**

Disable the other buttons when `isRunning` is false or `gameOver` is true. 

Check your work against the solution below...

-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-

```js
{/* left */}
<button 
  disabled={!isRunning || gameOver}
  className="control-button" 
  onClick={(e) => {
    dispatch(moveLeft())
  }
}>Left</button>

{/* right */}
<button 
  disabled={!isRunning || gameOver}
  className="control-button" 
  onClick={(e) => {
    dispatch(moveRight())
  }
}>Right</button>

{/* rotate */}
<button 
  disabled={!isRunning || gameOver}
  className="control-button" 
  onClick={(e) => {
    dispatch(rotate())
  }
}>Rotate</button>

{/* down */}
<button 
  disabled={!isRunning || gameOver}
  className="control-button" 
  onClick={(e) => {
    dispatch(moveDown())
  }
}>Down</button>
```

With these changes, the controls will not work when the game is paused or over. You haven't solved the game-over situation yet, soon! For now when the game is paused the blocks should not move or rotate when the controls are used. 

# Detecting a game over

We can detect a game over in the `addBlockToGrid`
function `utils/index.js`

The `addBlockToGrid()` function adds a block to the grid. If part of the block ends up off the top of the grid the game is over.

The goal is to have the function return the new grid and a bool that says the game is over (true) or the game is not over (false). Since we can only return one value from a function, the return type will have to change to an object with two properties: `grid` and `gameOver`.

**Optional Challenge**

This is a tough one. Give it a try! You need to modify `addBlockToGrid` so that when placing a block if part of that block is off the top of the grid the game is over. 

The function needs to return both the newly updated grid and a boolean designating the game over status. Normally you can't return more than one value so you should return a "tuple". JS doesn't have an offical tuple, you can use an object or an array. An object might look like this: `{ newGrid, gameOver }`. 

Here is what you have currently, with a couple added hints: 

```JS
export const addBlockToGrid = (shape, grid, x, y, rotation) => {
  // At this point the game is not over
  let gameOver = false // Assume the gamne is not over
  // Get the block array
  const block = shapes[shape][rotation];
  // Copy the grid
  const newGrid = [...grid];            
  // Map the Block onto the grid                                                           
  for (let row = 0; row < block.length; row++) {
    for (let col = 0; col < block[row].length; col++) {
      if (block[row][col]) {
        // Subtract the y index from the row if the value is 
        // less than 0 the block extends off the top of the 
        // grid and the game is over. 

        // ?

        newGrid[row + y][col + x] = shape;
      }
    }
  }
  // Return a tuple with grid and gameover
  return { newGrid, gameOver };
}
```

Note: This change will break the `moveDown` reducer in `src/features/gameSlice.js`. You'll need to modify this also! 

Check your work against the solution below...

-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-

In `/src/utils/index.js`, update `addBlockToGrid` to the following:

```JavaScript
// Adds current shape to grid
export const addBlockToGrid = (shape, grid, x, y, rotation) => {
  // At this point the game is not over
  let gameOver = false
  const block = shapes[shape][rotation]
  const newGrid = [ ...grid ]
  for (let row = 0; row < block.length; row++) {
    for (let col = 0; col < block[row].length; col++) {
      if (block[row][col]) {
        const yIndex = row + y
        // If the yIndex is less than 0 part of the block
        // is off the top of the screen and the game is over
        if (yIndex < 0) {
          gameOver = true
        } else {
          newGrid[row + y][col + x] = shape
        }
      }
    }
  }
  // Return both the newGrid and the gameOver bool                                                
  return { newGrid, gameOver }
}
```

Remember with these changes you have broken the `moveDown` reducer. Which will require an update. 

# Modify the game-reducer

Modify the `moveDown` case for `/src/features/gameSlice.js`:

```JS
...
moveDown: (state) => {
  const { x, y, shape, grid, rotation, nextShape } = state
  // Get the next potential Y position
  const maybeY = y + 1
  // Check if the current block can move here
  if (canMoveTo(shape, grid, x, maybeY, rotation)) {
    // If so move the block
    state.y = maybeY
    return state
  }

  // ==============EDIT BEGIN================
  // If not place the block
  const { newGrid, gameOver } = addBlockToGrid(shape, grid, x, y, rotation)
  if (gameOver) {
    state.gameOver = true
    return state
  }
  // ==============EDIT END=================
  
  // reset some things to start a new shape/block
  state.x = 3
  state.y = -4
  state.rotation = 0
  state.grid = newGrid
  state.shape = nextShape
  state.nextShape = randomShape()

  if (!canMoveTo(nextShape, newGrid, 0, 4, 0)) {
    // Game Over
    console.log("Game Should be over...")
    state.shape = 0
    state.gameOver = true
    return state
  }

  // Update the score based on if rows were completed or not
  state.score += checkRows(newGrid)
    return state
  },
  ...
```

Try to force a game over and make sure you see the popup:

![gameover](assets/gameover.png)

Almost done! One more feature and we've got a full Tetris game!

# Now Commit

>[action]
>
```bash
$ git add .
$ git commit -m 'game over implemented'
$ git push
```

# Restart Game-Reducer

Now that the game knows when it's ended, we need a way to restart it!

Restarting the game will only require setting the game `state` in redux back to default, and there already is a function for this!

**Challenge**

Find the `restart` action and fill in its reducer function. You want to return the default state of the game by calling the `defaultState` function from utils. 

Check your work against the solution below...

-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-

In `src/features/gameSlice.js` find:

```JS
restart: () => {}
```

Change it to this: 

```JS
restart: () => defaultState()
```

Yep, it can be that simple. Here you called `defaultState()` it returned the state object and you returned that from your reducer. Since this is an arrow function and the code block is on one line you can omit the `{}` and `return` keywords. 

# Now Commit

```bash
$ git add .
$ git commit -m 'restart implemented'
$ git push
```

**You have now implemented an advanced single-page application with React — a fully functioning Tetris game! Congrats!**

# Further Challenges

Now that you have your game working why not spend some time making it more interesting along the way you can review what you learned and reinforce force the concepts by trying these challenges! 

There are a lot of extra challenges listed below. You can pick and choose to do anything from the list you don't have to do them in order. Tackle whatever interests you! 

## Publish your game to GitHub pages

This is a React project so it needs a little bit more work than just pushing to GitHub.com. 

Follow the guide here: https://github.com/gitname/react-gh-pages#3-install-the-gh-pages-npm-package

You can start at step 3 since you've already created the React project and pushed it to GitHub. 

## Change the styles 

The square and the buttons use a beveled/chiseled look which doesn't look that great. This was chosen originally because it looks like the original game. But you can add a more modern and updated look with a few styles. 

Here are a few ideas to get you started. 

Remove the border on the square; 

```CSS
.grid-square {
  /* border-style: solid; */
  width: var(--tile-size);
  height: var(--tile-size);
  /* border-width: var(--border-width);
  border-left-color: var(--border-left-color);
  border-top-color: var(--border-top-color);
  border-right-color: var(--border-right-color);
  border-bottom-color: var(--border-bottom-color); */
}
```

Add a border-radius 

```CSS
/* Grid Square */
.grid-square {
  width: var(--tile-size);
  height: var(--tile-size);
  border-radius: 4px;
}
```

Add a gap between the grid squares by adding `grid-gap` to the game board styles. 

```CSS
/* Grid Board */
.grid-board {
  display: grid;
  grid-template-columns: repeat(var(--cols), var(--tile-size));
  grid-gap: 0;
  align-self: flex-start;
  grid-area: c;
  grid-gap: 1px; /* Add a gap! */
}
```

You can apply the same ideas to the next block. 

Style the buttons: 

```CSS
/* Score Board */
.score-board-button {
  display: block;
  padding: 1em;
  background-color: var(--color-0);
  border: none;
  /* border-width: 5px; */
  /* border-top-color: var(--button-color-t);
  border-left-color: var(--button-color-l);
  border-right-color: var(--button-color-r);
  border-bottom-color: var(--button-color-b); */
}
```

You can do the same thing to the `.control-button`. Might be good to make a `.button` class and share this across both the scoreboard and control buttons. This was overlooked in the original tutorial.

The buttons in the scoreboard are a little uneven. It would be good to arrange to make these buttons so they fill the width of the sidebar. You can do this with flex box. 

```CSS
/* score-board */
.score-board {
  grid-area: r;
  display: flex;
  flex-direction: column;
}
```

You can customize the colors by adjusting the color variables at the top of the style sheet: 

```CSS
:root {
  --bg-color: rgba(150, 150, 150, 1);

  /* Border Colors are all transparent colors. These will tint or shade the background color of the square. */
  --border-left-color: rgba(255, 255, 255, 0.20);
  --border-top-color: rgba(255, 255, 255, 0.33);
  --border-right-color: rgba(0, 0, 0, 0.15);
  --border-bottom-color: rgba(0, 0, 0, 0.5);

  /* Square Colors: background colors for the squares.*/
  --color-0: #eaeaea;
  --color-1: #ff6600;
  --color-2: #eec900;
  --color-3: #0000ff;
  --color-4: #cc00ff;
  --color-5: #00ff00;
  --color-6: #66ccff;
  --color-7: #ff0000;
  ...
}
```

## JS Challenges 

Looks like the next block displays the color of the shape as color 0. It would be nice to display the shape and color. Colors are mapped to an index and the index matches the index of the shape. 

The problem is the square value is 0 or 1. It's the shape index `nextShape` that should be an index of the color. 

```JS
export default function NextBlock(props) {
  ...
  const box = shapes[nextShape][0]

  const grid = box.map((rowArray, row) => {
    return rowArray.map((square, col) => {
    // make the changes here: 
      return <GridSquare key={`${row}${col}`} color={square === 0 ? 0 : nextShape} />
    })
  })
  ...
}
```

Note the change here sets the color to 0 if the `square` value is 0 or the `nextShape`. 

## Counting Completed Rows 

Besides scoring points, you also remove rows of squares. Tracking the number of rows removed can be used to track the score.

In some versions of the game, the goal is to complete several rows within a given time allotment. You could use the completion of rows to decrease the speed property on state increasing the speed of the game and making play more difficult over time. 

To track the completed rows you might follow these steps: 

- Define a new property on the state for rows Completed. This will need to go into the default state object in `utils/index.js`
- The checkRows function calculates the rows completed and returns a score. You'll need to modify this to return the score and the number of rows completed.
- In the `features/gameSlice.js` take a look at the code in `moveDown` case. Near the bottom, the code calls `checkRows()` if you modified the return value work with that here. Also, calculate the new row count here and set it on newState. 

Add this to `utils.js`. 

```JS
// Checks for completed rows and scores points
export const checkRows = (grid) => {
  // Points increase for each row completed
  // i.e. 40 points for completing one row, 100 points for two rows
  const points = [0, 40, 100, 300, 1200]
  let completedRows = 0
  for (let row = 0; row < grid.length; row++) {
    // No empty cells means it can't find a 0, so the row must be complete!
    if (grid[row].indexOf(0) === -1) {
      completedRows += 1
      // Remove the row and add a new empty one at the top
      grid.splice(row, 1)
      grid.unshift(Array(10).fill(0))
    }
  }
  return points[completedRows]
}
```

This function takes the grid array as an argument, removes completed rows, and returns the number of points gained. 

You can import this into your `gameSlice.js` call it at the bottom of `moveDown`. Add this at the end of the function function before the return statement. 

```JS
// Update the score based on if rows were completed or not
state.score += checkRows(newGrid)
```

## Edge Cases 

The game has a bug. When a block is off the top edge of the grid if you move it left or right enough to move it off the grid the game registers this as a game over. You can try this yourself. When a block starts clicking left or right enough will cause a game over. You won't see the block. 

To track down the problem think about how the game works. Click the left or right button you're issuing an action which is in turn sent to the reducer. In the reducer, a `moveRight` or `moveLeft` action call `canMoveTo()` returns true or false if the shape can be moved. There is a problem here. 

Here is a refactor of the canMoveTo function. Try this on your own. You can compare your solution. 

```JS
export const canMoveTo = (shape, grid, x, y, rotation) => {
  const currentShape = shapes[shape][rotation]
  // Get the width and height of the grid
  const gridWidth = grid[0].length - 1
  const gridHeight = grid.length - 1
  // Loop over the shape array
  for (let row = 0; row < currentShape.length; row++) {
    for (let col = 0; col < currentShape[row].length; col++) {
      // If the value isn't 0 it's part of the shape
      if (currentShape[row][col] !== 0) {
        // Offset the square to map it to the larger grid
        const proposedX = col + x
        const proposedY = row + y
        // Get the possible row. This might be undefined if we're above the top
        const possibleRow = grid[proposedY]

        // Off the left or right side or the bottom return false
        if (proposedX < 0 || proposedX > gridWidth || proposedY > gridHeight) {
          return false
        } else if (possibleRow !== undefined) {
          // If the row is not undefined you're on the grid
          if (possibleRow[proposedX] !== 0) {
            // This square must be filled
            return false
          }
        }
      }
    }
  }
  return true
}
```

## User interactions 

The game is working but the user interface needs some attention. While clicking the buttons works it would be better and more playable to be able to use the keyboard.

Think about which keys would make sense for controls. The left and right arrows make sense to work with the left and right actions. The rotate action might be the space bar or the down and or the up arrow. The W, A, S, and D keys are also classic controls for games. 

using the keyboard you could introduce a positive and negative rotation. Currently, the rotation is only in the positive direction. There is only one button that limits how the game can handle rotation. With the key board, the down arrow could rotate in the positive direction and the up arrow could rotate in the negative direction. 

## Saving High Scores 

If you did the other React + Redux tutorials you worked with Local storage you could put these ideas to use here to save the game state and save the high score. 
