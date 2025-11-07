# Chess WebSocket API

A real-time multiplayer chess game using WebSocket connections.

## Setup

1. Install dependencies:
```bash
npm install
```

2. Build the TypeScript code:
```bash
tsc
```

3. Start the server:
```bash
node dist/index.js
```

Server will start on `ws://localhost:8080`

## How It Works

### Chess Board Layout

```
  a  b  c  d  e  f  g  h
8 ♜ ♞ ♝ ♛ ♚ ♝ ♞ ♜  ← Black pieces (rank 7-8)
7 ♟ ♟ ♟ ♟ ♟ ♟ ♟ ♟
6 · · · · · · · ·
5 · · · · · · · ·
4 · · · · · · · ·
3 · · · · · · · ·
2 ♙ ♙ ♙ ♙ ♙ ♙ ♙ ♙  ← White pieces (rank 1-2)
1 ♖ ♘ ♗ ♕ ♔ ♗ ♘ ♖
```

- **White pieces**: Ranks 1-2 (bottom)
- **Black pieces**: Ranks 7-8 (top)
- **White always moves first**

## Message Types

### 1. Initialize Game
Send this to join/start a game:
```json
{
  "type": "init_game"
}
```

**Response** (you'll receive):
```json
{
  "type": "init_game",
  "payload": {
    "color": "white"  // or "black"
  }
}
```

### 2. Make a Move
```json
{
  "type": "move",
  "move": {
    "from": "e2",
    "to": "e4"
  }
}
```

**Response** (opponent receives):
```json
{
  "type": "move",
  "payload": {
    "from": "e2",
    "to": "e4"
  }
}
```

### 3. Game Over
When game ends, both players receive:
```json
{
  "type": "game_over",
  "payload": {
    "winner": "white"  // or "black"
  }
}
```

## Testing with Hopscotch (or any WebSocket client)

### Test Scenario: Complete Game Flow

#### **Player 1 (Will be White)**

1. Connect to `ws://localhost:8080`

2. Initialize game:
```json
{"type": "init_game"}
```

3. Wait for Player 2 to connect...

4. You'll receive:
```json
{"type": "init_game", "payload": {"color": "white"}}
```

5. Make first move (white moves first):
```json
{
  "type": "move",
  "move": {
    "from": "e2",
    "to": "e4"
  }
}
```

6. Wait for black's response...

7. Make next move:
```json
{
  "type": "move",
  "move": {
    "from": "g1",
    "to": "f3"
  }
}
```

#### **Player 2 (Will be Black)**

1. Connect to `ws://localhost:8080`

2. Initialize game:
```json
{"type": "init_game"}
```

3. You'll receive:
```json
{"type": "init_game", "payload": {"color": "black"}}
```

4. You'll also receive white's first move:
```json
{"type": "move", "payload": {"from": "e2", "to": "e4"}}
```

5. Respond with your move:
```json
{
  "type": "move",
  "move": {
    "from": "e7",
    "to": "e5"
  }
}
```

6. You'll receive white's next move:
```json
{"type": "move", "payload": {"from": "g1", "to": "f3"}}
```

## Example: Full Opening Sequence

```json
// White
{"type": "move", "move": {"from": "e2", "to": "e4"}}

// Black
{"type": "move", "move": {"from": "e7", "to": "e5"}}

// White
{"type": "move", "move": {"from": "g1", "to": "f3"}}

// Black
{"type": "move", "move": {"from": "b8", "to": "c6"}}

// White
{"type": "move", "move": {"from": "f1", "to": "c4"}}

// Black
{"type": "move", "move": {"from": "g8", "to": "f6"}}
```


- **King**: Any direction 1 square
  - Example: `e1 → e2`, `e8 → e7`


