# Lab 2 – Parallel Minimax for Dots and Boxes

## Overview
This project implements both **sequential** and **parallelized** versions of the Minimax algorithm for the game *Dots and Boxes*.  
The parallel version uses Java's `ExecutorService` with a fixed thread pool to evaluate independent root moves concurrently, resulting in significant speedups for large search trees.

---

## Motivation
We wanted an agent that could consistently beat human opponents at *Dots and Boxes* (popular on GamePigeon).  
Playing against a strong computer player provides practice for competitive games with friends.  

Our initial **Greedy Bot** simply chose the move that captured the most boxes at the current turn.  
While it beat a random move agent ~85% of the time, it was **not optimal** and often lost when it set up the opponent for a bigger score.  

The Minimax algorithm, on the other hand, considers the long-term consequences of moves, leading to much stronger play.

---

## Minimax Algorithm
- **Player A (Maximizer):** Chooses the move that maximizes their eventual score.
- **Player B (Minimizer):** Chooses the move that minimizes Player A's eventual score.
- Searches the game tree to a fixed depth and returns the best move assuming optimal play from both players.

In testing, Minimax (depth 3) beat Greedy Bot in **92.5% of 1000 trials** on a 5x5 board.

---

## Parallelization
We applied **coarse-grained exploratory decomposition**:
- **Game tree = search space**.
- Each **possible root move** becomes an independent task.
- Threads run Minimax from their assigned starting move.
- Results are collected and the best move is chosen.

**Threading Implementation (Java):**
- Uses `ExecutorService` with a fixed thread pool.
- Number of threads = number of available processors.
- One `Callable` per root move, returning `(move, score)` pairs.
- `invokeAll()` runs tasks in parallel and blocks until all are complete.
