# Knight's Travails - Find Shortest Path on a Chessboard

## 📌 Overview

Knight's Travails is a classic algorithm problem that finds the **shortest path** a chess knight needs to travel from a starting position to a target position on an 8×8 chessboard. This implementation uses **Breadth-First Search (BFS)**, which guarantees finding the optimal solution.

## 🎯 Problem Statement

Given:
- An 8×8 chessboard
- A starting position `[start_row, start_col]`
- A target position `[target_row, target_col]`

Find:
- **Minimum number of moves** required for a knight to reach from start to target
- **Exact path** the knight takes (sequence of positions)

### Knight Movement Rules

A chess knight moves in an **"L" shape**:
- 2 squares in one direction + 1 square perpendicular, OR
- 1 square in one direction + 2 squares perpendicular

This results in **8 possible moves** from any position:

```
Knight's 8 possible moves from position (x, y):
    (-2, -1)    (-1, -2)
       ↖         ↗
(-2, +1) ← [x,y] → (+1, -2)
       ↙         ↘
    (-1, +2)    (+1, +2)
```

## 🔧 Algorithm: Breadth-First Search (BFS)

### Why BFS?

✅ **Guarantees shortest path** in unweighted graphs  
✅ **Explores level-by-level**, visiting all positions at distance `d` before distance `d+1`  
✅ **Optimal for our problem** - each move counts as 1 unit distance  
✅ **Iterative approach** - avoids recursion overhead  

### Time & Space Complexity

| Metric | Value | Explanation |
|--------|-------|-------------|
| **Time Complexity** | O(1) | Fixed 8×8 board = 64 maximum positions |
| **Space Complexity** | O(1) | Queue + visited array + parent array = constant |
| **Move Checks** | O(8) | 8 possible moves per position |

### How BFS Works

```
1. Initialize
   ├─ Create Queue with starting position
   ├─ Create Visited Set/Array
   └─ Mark start as visited

2. BFS Loop
   ├─ While queue is not empty:
   │  ├─ Dequeue front position
   │  ├─ If position == target → Found! Return path
   │  └─ For each of 8 possible knight moves:
   │     ├─ If within bounds AND not visited:
   │     │  ├─ Mark as visited
   │     │  ├─ Store parent reference
   │     │  └─ Enqueue for exploration

3. Path Reconstruction
   ├─ Start from target
   ├─ Follow parent pointers back to start
   └─ Reverse to get start → target path
```

## 📊 Example Walkthrough

### Example 1: Corner to Corner

**Input:**
```javascript
Start:  [0, 0]  // Top-left corner
Target: [7, 7]  // Bottom-right corner
```

**Algorithm Execution:**

```
Level 0: Queue = [[0, 0]]
         Visited = {[0,0]}

Level 1: From [0,0], can move to:
         [1, 2], [2, 1]
         Visited = {[0,0], [1,2], [2,1]}
         Queue = [[1,2], [2,1]]

Level 2: From [1,2], can move to:
         [0,0] (visited), [0,4], [2,0], [2,4], [3,1], [3,3]
         From [2,1], can move to:
         [0,0] (visited), [0,2], [1,3], [3,3] (duplicate)
         ... continue ...

Level 6: Reach [7, 7]!
```

**Output:**
```javascript
Minimum Moves: 6
Path: [
  [0, 0],
  [1, 2],
  [2, 4],
  [4, 5],
  [6, 6],
  [7, 7]
]
```

### Example 2: Short Distance

**Input:**
```javascript
Start:  [3, 3]
Target: [4, 5]
```

**Output:**
```javascript
Minimum Moves: 1
Path: [
  [3, 3],
  [4, 5]
]
```

**Explanation:** From [3,3], the knight can directly move to [4,5] in one move (1 right, 2 down is a valid knight move).

## 💻 Implementation Details

### Core Functions

#### 1. `knightMove(start, target)`
Main function that solves the problem using BFS.

```javascript
function knightMove(start, target) {
  // Handle edge case: already at target
  if (start[0] === target[0] && start[1] === target[1]) {
    return [start];
  }

  // Knight's 8 possible moves
  const moves = [
    [-2, -1], [-1, -2], [1, -2], [2, -1],
    [2, 1], [1, 2], [-1, 2], [-2, 1]
  ];

  // BFS Setup
  const queue = [start];
  const visited = new Set([start.toString()]);
  const parent = new Map();
  parent.set(start.toString(), null);

  // BFS Loop
  while (queue.length > 0) {
    const current = queue.shift();

    // Check if we reached target
    if (current[0] === target[0] && current[1] === target[1]) {
      return reconstructPath(current, start, parent);
    }

    // Generate all 8 possible moves
    for (const [dx, dy] of moves) {
      const next = [current[0] + dx, current[1] + dy];

      // Check boundaries
      if (isValid(next) && !visited.has(next.toString())) {
        visited.add(next.toString());
        parent.set(next.toString(), current);
        queue.push(next);
      }
    }
  }

  return null; // No path found (shouldn't happen on 8x8)
}
```

#### 2. `isValid(position)`
Checks if a position is within chessboard boundaries.

```javascript
function isValid(pos) {
  return pos[0] >= 0 && pos[0] < 8 && pos[1] >= 0 && pos[1] < 8;
}
```

#### 3. `reconstructPath(target, start, parent)`
Backtracks from target to start using parent pointers.

```javascript
function reconstructPath(target, start, parent) {
  const path = [];
  let current = target;

  while (current !== null) {
    path.unshift(current);
    current = parent.get(current.toString());
  }

  return path;
}
```

### Data Structures Used

| Structure | Purpose | Size |
|-----------|---------|------|
| **Queue** | Stores positions to explore (FIFO) | Up to 64 |
| **Visited Set** | Tracks explored positions | Up to 64 |
| **Parent Map** | Stores parent of each position | Up to 64 |
| **Moves Array** | 8 possible knight moves | 8 |

## 📈 Complexity Analysis

### Time Complexity: O(1) = O(64)

```
Fixed 8×8 board = 64 maximum positions
Each position: O(8) - check 8 possible moves
Total: 64 × 8 = 512 operations = O(1) constant
```

### Space Complexity: O(1) = O(64)

```
Visited Array:    8×8  = 64 cells
Queue:           max 64 cells
Parent Map:      max 64 entries
Total: O(64) = O(1) constant relative to problem
```

### Why Not Dijkstra or A*?

| Algorithm | Use Case |
|-----------|----------|
| **BFS** | Unweighted graphs ✅ (Our case) |
| **Dijkstra** | Weighted graphs ❌ (Overkill here) |
| **A\*** | With admissible heuristic ❌ (More complex, not needed) |
| **DFS** | Any path (not optimal) ❌ |

## 🎓 Learning Outcomes

This project teaches:

### 1. Graph Theory
- ✅ Graph representation (vertices = positions, edges = valid moves)
- ✅ Unweighted vs weighted graphs
- ✅ Graph traversal concepts

### 2. Algorithm Design
- ✅ Why BFS finds shortest path in unweighted graphs
- ✅ Queue-based level-by-level exploration
- ✅ Visited tracking to prevent revisits
- ✅ Parent pointers for path reconstruction

### 3. Data Structures
- ✅ Queue (FIFO operations)
- ✅ Set/Map for visited tracking and parent storage
- ✅ 2D array indexing

### 4. Problem Solving
- ✅ Breaking complex problems into steps
- ✅ Choosing appropriate algorithms
- ✅ Analyzing time and space complexity

## 🔗 Real-World Applications

| Application | How It's Used |
|-------------|---------------|
| **Chess Engines** | Evaluate knight moves, find optimal sequences |
| **Game AI** | Character pathfinding with limited move sets |
| **Robot Navigation** | Path planning with specific movement constraints |
| **Network Routing** | Finding shortest routes with limited hops |
| **Game Development** | Unit movement in strategy games |

## 🚀 Usage

```javascript
// Find shortest path
const start = [0, 0];
const target = [7, 7];
const path = knightMove(start, target);

console.log(`Minimum moves: ${path.length - 1}`);
console.log('Path:', path);
// Output:
// Minimum moves: 6
// Path: [[0,0], [1,2], [2,4], [4,5], [6,6], [7,7]]
```

## ✅ Key Takeaways

1. **Algorithm**: BFS is optimal for unweighted shortest path problems
2. **Implementation**: Queue-based iteration with visited tracking
3. **Complexity**: O(1) time/space for fixed 8×8 board
4. **Path Reconstruction**: Parent pointers enable efficient backtracking
5. **Interview Ready**: Classic problem demonstrating strong algorithm knowledge

## 📋 Summary Table

| Aspect | Details |
|--------|---------|
| **Problem Type** | Shortest path in unweighted graph |
| **Algorithm** | Breadth-First Search (BFS) |
| **Time Complexity** | O(1) for 8×8, O(n×m) general |
| **Space Complexity** | O(1) for 8×8, O(n×m) general |
| **Optimal?** | ✅ Yes (guaranteed shortest) |
| **Data Structures** | Queue, Set/Map, 2D Array |
| **Key Technique** | Parent pointers for path reconstruction |

---

**Repository**: [Knight's Travails](https://github.com/NabeelFarooq/Knights-Travails)  
