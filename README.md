# KIIT Campus Explorer — Graph Traversal Visualizer

> An interactive, browser-based visualizer that maps all 17 KIIT University campuses as a weighted graph and animates six classic graph traversal and pathfinding algorithms step by step.

---

## Overview

This project turns the KIIT University campus network into a live graph problem. Each campus is a **node**, physical connections between campuses are **edges**, and edge weights represent approximate walking time in minutes. You can watch any of six algorithms explore or find paths through this network in real time, with full state visibility at every step.

Built as a single self-contained HTML file — no dependencies, no build step, no server required.

---

## Campuses (Nodes)

| Code | Campus | Zone |
|------|--------|------|
| C-1  | Engineering & IT | North |
| C-2  | Mech & Civil | North |
| C-3  | Biotechnology | North |
| C-4  | Law | North |
| C-5  | KSOM Management | North |
| C-6  | KISS | North |
| C-7  | Architecture | South |
| C-8  | Fashion Design | South |
| C-9  | Dental | South |
| C-10 | Film & Media | South |
| C-11 | Medical | South |
| C-12 | Hospitality | West |
| C-13 | Sports Complex | West |
| C-14 | Research & Innovation | North |
| C-15 | School of Education | West |
| C-16 | Mass Communication | South |
| C-17 | International Studies | South |

**Graph stats:** 17 nodes · 35 edges · weighted (1–6 min) · undirected

---

## Algorithms

### Uninformed Search

These algorithms have no knowledge of where the goal is and explore purely based on graph structure.

#### BFS — Breadth-First Search
- **Data structure:** Queue (FIFO)
- **Strategy:** Explores all neighbors at the current depth before going deeper
- **Guarantees:** Shortest path in terms of hop count (unweighted)
- **Use when:** You want to visit every campus level by level from the start

#### DFS — Depth-First Search
- **Data structure:** Stack (LIFO)
- **Strategy:** Dives as deep as possible along one path before backtracking
- **Guarantees:** Will visit all reachable nodes, but not in optimal order
- **Use when:** You want to explore one route to its end before trying another

#### UCS — Uniform Cost Search
- **Data structure:** Priority queue ordered by cumulative path cost
- **Strategy:** Always expands the node with the lowest total cost so far
- **Guarantees:** Optimal path by cumulative edge weight (equivalent to Dijkstra for single-destination queries)
- **Use when:** Edge weights matter and you want the cheapest route

---

### Informed / Heuristic Search

These algorithms use a **heuristic function h(n)** — the straight-line Euclidean distance between a node and the goal, scaled to the edge weight domain — to guide their search.

#### Greedy Best-First Search
- **Data structure:** Priority queue ordered by h(n) only
- **Strategy:** Always expands whichever node looks closest to the goal by straight-line distance
- **Guarantees:** Fast, but NOT guaranteed to find the optimal path
- **Use when:** You want a quick approximate path and optimality is not critical
- **Watch out for:** Greedy can get trapped by local minima and take longer routes than A*

#### A\* (A-Star)
- **Data structure:** Priority queue ordered by f(n) = g(n) + h(n)
  - g(n) = actual cost from start to current node
  - h(n) = estimated cost from current node to goal (heuristic)
  - f(n) = total estimated cost of path through current node
- **Guarantees:** Optimal path, provided the heuristic is admissible (never overestimates) — which Euclidean distance is
- **Use when:** You need the shortest weighted path to a specific campus
- **Why it beats Greedy:** Balances what it already knows (g) with what it estimates (h), avoiding costly detours

---

### All-Pairs Shortest Path

#### Dijkstra's Algorithm
- **Data structure:** Priority queue ordered by distance
- **Strategy:** Greedily settles the globally cheapest unvisited node at every step
- **Guarantees:** Optimal shortest distances from the start node to ALL other nodes simultaneously
- **Use when:** You want to compute the full distance map from one campus to every other campus
- **Difference from UCS:** Conceptually equivalent, but Dijkstra is framed as computing all distances, while UCS typically targets a single goal

---

## How to Use

### Running the project

Open `kiit-campus-explorer.html` in any modern browser. No installation required.

```
# macOS / Linux
open kiit-campus-explorer.html

# Windows
start kiit-campus-explorer.html
```

### Controls

| Control | Description |
|---------|-------------|
| **Algorithm dropdown** | Select the traversal algorithm |
| **Start Campus** | Set the source node for traversal |
| **Goal Campus** | Set the destination (shown only for A* and Greedy BFS) |
| **Play / Pause** | Auto-animate the algorithm at the current speed |
| **Step →** | Advance exactly one step at a time |
| **Reset** | Clear the visualization and restart |
| **Speed slider** | Adjust animation delay (80ms – 2000ms per step) |
| **Click a node on map** | Instantly sets that campus as the start node |

### Reading the visualization

**Node colors:**

| Color | Meaning |
|-------|---------|
| Dark blue-gray | Unvisited — not yet discovered |
| Purple | In the frontier (queue / stack / priority queue) |
| Cyan | Currently being processed |
| Green | Fully visited and settled |
| Orange | Goal node (A* / Greedy BFS only) |

**Edge colors:**

| Color | Meaning |
|-------|---------|
| Dark gray | Untraversed |
| Purple | Adjacent to a frontier node |
| Cyan | Currently active edge |
| Green | Both endpoints visited |
| Orange | Part of the discovered path (A* / Greedy BFS) |

### Right panel

- **Frontier display** — live contents of the queue / stack / priority queue with scores
- **Traversal order** — numbered circles showing the order campuses were visited
- **Score table** — for cost-based algorithms: shows current distance / f-score and predecessor node for every campus
- **Path Found** — for A* and Greedy BFS: displays the complete path and total cost once the goal is reached
- **Current Step** — plain-English description of what the algorithm just did

---

## Algorithm Comparison

| Algorithm | Optimal? | Complete? | Uses Heuristic? | Time Complexity |
|-----------|----------|-----------|-----------------|-----------------|
| BFS | Yes (unweighted) | Yes | No | O(V + E) |
| DFS | No | Yes | No | O(V + E) |
| UCS | Yes | Yes | No | O((V + E) log V) |
| Greedy BFS | No | Yes* | Yes (h only) | O((V + E) log V) |
| A* | Yes | Yes | Yes (g + h) | O((V + E) log V) |
| Dijkstra | Yes | Yes | No | O((V + E) log V) |

*Complete on finite graphs without repeated states.

---

## Project Structure

```
kiit-campus-explorer.html
└── Everything in one file
    ├── CSS          Dark-theme UI with transition animations
    ├── SVG map      17 campus nodes + 35 weighted edges
    ├── Graph data   Adjacency list built from EDGES array
    ├── Generators   Each algorithm is a JS generator function
    └── Engine       Step / Play / Reset loop + UI renderer
```

### Extending the graph

To add a new campus, append an entry to the `C` array and add edges to `EDGES`:

```javascript
// In the C array:
{ id: 17, code: "C-18", name: "New Campus", x: 300, y: 200 }

// In the EDGES array (format: [nodeA, nodeB, weight]):
[17, 0, 4],   // C-18 connects to C-1 with weight 4
[17, 5, 6],   // C-18 connects to C-6 with weight 6
```

The map, selectors, and all algorithms update automatically.

---

## Technical Notes

- **Heuristic function:** Euclidean pixel distance between nodes, divided by 68 to normalize to the edge weight scale. This makes the heuristic admissible.
- **Generator pattern:** Each algorithm is a JavaScript `function*` generator. The engine calls `.next()` on each step/tick, making Play/Pause/Step trivial to implement without async complexity.
- **No external dependencies:** Pure HTML + CSS + vanilla JavaScript. Fonts load from Google Fonts (requires internet); the rest works fully offline.
- **Browser support:** All modern browsers (Chrome, Firefox, Safari, Edge). Uses SVG, CSS custom properties, and ES6 generators.

---

## Built For

**KIIT University, Patia, Bhubaneswar, Odisha** — as an educational tool for visualizing and understanding graph traversal algorithms in the context of a real-world campus network.
