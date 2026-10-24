# The Sun Orb

A shared, 3D stone-ball twist puzzle in Maya style, made as a table prop for a tabletop game. The ball is built from 24 carved stones in six colors, each with its own glyph. Everyone with the link turns the **same orb at the same time**. Restore every side to a single color and glyph and dust falls from the ceiling, then the orb splits open like a gate onto a world of light.

**Play it:** https://ashtabletop.com/sealed-orb-puzzle/

## How to play

- **Turn:** press a stone and drag along the ball. Half of the orb turns with it.
- **Look around:** drag the empty space around the ball (or use the arrow keys) to spin the whole orb. This only changes your own view.
- **Goal:** every side of the ball shows one color and one glyph.
- Every color has its own glyph, so the puzzle works for color-blind players.

## For the DM

Open **DM controls** under the orb to:

- Copy the player link.
- Start a new puzzle (Easy, Medium, or Hard) or restart the current one, for everyone at once.
- Set the message that appears when the gate opens.

To run a separate orb for another group, add `?room=name` to the link, for example `.../sealed-orb-puzzle/?room=thursday`.

## How the sharing works

The puzzle state (the starting scramble and every turn) is stored in a Firebase Realtime Database under `orbRooms/<room>`, and every screen replays it. If the database cannot be reached, the page falls back to solo mode.

The database rules for `orbRooms` only accept well-formed turns:

```json
"orbRooms": {
  "$room": {
    ".read": true,
    ".write": "$room.matches(/^[a-z0-9-]{1,24}$/)",
    "id": { ".validate": "newData.isString() && newData.val().length <= 24" },
    "level": { ".validate": "newData.isNumber() && newData.val() >= 1 && newData.val() <= 12" },
    "reveal": { ".validate": "newData.isString() && newData.val().length <= 200" },
    "scramble": { "$i": { ".validate": "newData.hasChildren(['a','side','dir'])", "a": { ".validate": "newData.isNumber() && newData.val() >= 0 && newData.val() <= 2" }, "side": { ".validate": "newData.val() === 1 || newData.val() === -1" }, "dir": { ".validate": "newData.val() === 1 || newData.val() === -1" }, "$other": { ".validate": false } } },
    "moves": { "$k": { ".validate": "newData.hasChildren(['a','side','dir'])", "a": { ".validate": "newData.isNumber() && newData.val() >= 0 && newData.val() <= 2" }, "side": { ".validate": "newData.val() === 1 || newData.val() === -1" }, "dir": { ".validate": "newData.val() === 1 || newData.val() === -1" }, "$other": { ".validate": false } } },
    "$other": { ".validate": false }
  }
}
```

## Run it yourself

It's a single `index.html` file with no build step. Open it in any browser (it runs solo if it can't reach the database).

## Make it your own

The colors and carved glyphs live at the top of the script in `index.html` (`STONES` and `GLYPHS`).
