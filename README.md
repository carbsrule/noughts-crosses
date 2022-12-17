## Noughts & crosses

This is a tutorial for making a noughts and crosses game in JavaScript.

### Step 1: the game page

Start by creating a basic HTML document, as follows:

File: **index.html**
```
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Noughts and Crosses</title>
</head>

<body>
</body>
</html>
```

In the `<body>`, add a table which will show the game board. 
This will contain 3 rows (`<tr>`), each with 3 columns (`<td>`):

```
<table>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
</table>
```

In order to be able to change the look and feel of the page, you'll need some CSS.
Add a line in the `<head>` which points to the CSS rules you'll create.

```
<link href="game.css" rel="stylesheet" type="text/css">
```

Lastly, you'll need a JavaScript file which will run the actual game.
Add a line at the bottom of the `<body>` section which calls the script:

```
<script src="game.js"></script>
```

## Step Two: the board

In order to see the board, add some styles to the table, using [CSS](https://en.wikipedia.org/wiki/CSS).

File: **game.css**

```
table {border-collapse: collapse;}
td {
    /* Put a border around each game square */
    border: 1px solid #000;

    /* Set the size of each game square */
    width: 200px;
    height: 200px;
    
    /* Put each marker in the middle of its square */
    text-align: center;
    vertical-align: center;

    /* Make the markers big enough to match the square */
    font-size: 100px;
}
```

Open your web browser (e.g Firefox, Chrome, Safari) and use it to open your `index.html` file 

## Step Three: handle clicking on the board

Start with an infinite loop that controls the game:

File: **game.js**

Add a listener on each of the table cells to do something when a player clicks the game square:

```
let trs = document.getElementsByTagName('tr');
for (let row = 0; row < 3; ++row) {
    let tds = trs.item(row).getElementsByTagName('td');
    for (let col = 0; col < 3; ++col) {
        let td = tds.item(col);
        td.addEventListener('click', function() {
            updateCell(td, row, col);
        });
    }
}
```

Also add a basic function which will get called when a player clicks on a table cell:

```
function updateCell(td, row, col) {
    window.alert(`Clicked on row ${row}, col ${col}`);
}
```

Reload the `index.html` file in your browser and you should see the message "Clicked on row 1, col 1" when you click in the middle square (for example).

## Step Four: make the game work

Now add a representation of the board and current player in JavaScript.

At the top of your `game.js` file:

```
let board = [];
let player = 1;
```

Populate the board state to have all squares not yet assigned to a player.
Add this line before `let tds ...`

```
board[row] = [];
```

And add this line before `let td = ...`

```
board[row][col] = 0;
```

These two lines set up the board state to be a 3x3 grid, with each square having the value 0 (meaning the square hasn't been taken by a player).

Next, update your `updateCell` function to change the board state and change players when a square is clicked:

```
function updateCell(td, row, col) {
    // Only allow one player to mark a square
    if (board[row][col] > 0) {
        return;
    }

    // Update the representation of the board state
    board[row][col] = player;

    // Update the visual display of the board
    if (player == 1) {
        td.innerText = 'O';
    } else {
        td.innerText = 'X';
    }

    // Change player
    if (player == 1) {
        player = 2;
    } else {
        player = 1;
    }
}
```

Reload `index.html` in the browser and see that by taking turns, you now have a playable game. But there are a few more things to do before it's really finished.

## Step Five: finishing touches

Add a block to specify whose turn it is.

In the file **index.html**, add this at the start of the `<body>`, before the `<table>`

```
<h1>Player: <span id="player-id">O</span></h1>
```

Then, when one player has taken a turn, specify the other player.

Update this section in **game.js**:

```
// Change player
let specifiedPlayer = document.getElementById('player-id');
if (player == 1) {
    player = 2;
    specifiedPlayer.innerText = 'X';
} else {
    player = 1;
    specifiedPlayer.innerText = 'O';
}
```

Reload **index.html** in the browser. You'll now see that the game tells you whose turn it is, starting with the noughts player (O). But the game keeps playing after a player wins, so that's something to change.

Make some more changes in **game.js**:

After `let player = ...`, add another line to track whether the game is finished or not:

```
let finished = false;
```

Then update the `updateCell` function to only accept moves until the game has finished:

```
// Only allow one player to mark a square
// Players can't make moves once the game has finished
if (finished || board[row][col] > 0) {
```

Now before `// Change player` in the `updateCell` function, make the game check to see if there is a winner:

```
if (gameWon()) {
    finished = true;

    // Ensure final move appears on screen before displaying message
    window.setTimeout(function() {
        window.alert("You win!");
    }, 1);
    return;
}
```

Now add a `gameWon` function to check if the game has been won:

```
function gameWon() {
    // Check (horizontal) rows for 3 in a row
    for (let row = 0; row < board.length; ++row) {
        let player = board[row][0];
        if (player > 0 && board[row][1] == player && board[row][2] == player) {
            return true;
        }
    }

    // Check (vertical) columns for 3 in a row
    for (let col = 0; col < board.length; ++col) {
        let player = board[0][col];
        if (player > 0 && board[1][col] == player && board[2][col] == player) {
            return true;
        }
    }

    // Check top-left to bottom-right diagonal for 3 in a row
    let player = board[0][0];
    if (player > 0 && board[1][1] == player && board[2][2] == player) {
        return true;
    }

    // Check top-right to bottom-left diagonal for 3 in a row
    player = board[0][2];
    if (player > 0 && board[1][1] == player && board[2][0] == player) {
        return true;
    }

    // No 3 in a row found; game is still being played
    return false;
}
```

Now the game is playable and finishes once there is a winner.

Congratulations!

## Improvements

To make the game even better, you could:

* Highlight the winning squares (e.g. in a different colour)
* Add a "play again" button which appears once the game is finished. Pressing that button would clear the squares and restart the game, with the following steps:
 * Set the board state to 0 for each square
 * Clear the innerText of each `<td>` to reset the display
 * Set the player back to 1 and update the player text to "O"
 * Remove or hide the "play again" button (until the next game is finished)
