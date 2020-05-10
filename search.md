# Search
Some solutions to the problems tackled by AI can be thought as finding the **best path** in a [weighted graph](https://en.wikipedia.org/wiki/Graph_(discrete_mathematics)#Weighted_graph). Each node of the graph represents a possible state. Each edge represents a transition between states. Each edge weight represents the cost of the transition. A valid path is a sequence of connected nodes from a initial node to the goal node.

The best path is the one which is optimal according to some metric, usually the **total path cost**. For example, when solving a Rubik’s Cube, we want to go from a shuffled cube (initial state) to a solved cube (goal state). Each movement of the cube (transition) get us to an intermediate state. The best way to solve a cube is reaching the goal state with the lowest number of movements as possible.

## Breadth-first search
When the edges of the graph have all the same positive weight (or, equivalently, an unweighted graph), the simplest strategy to find an optimal path is to check if the initial state is the goal state. If not, then proceed to its children states, and then to its children’s children states, and so on, ignoring previously checked nodes until the goal is reached. This can be done adding (not previously checked) nodes in a **first-in-first-out (FIFO) queue**, called the **frontier**. Once we add the goal state to the frontier, we are guaranteed to have the optimal path. This algorithm is called **breadth-first search (BFS)**.

If we imagine BFS as a tree search algorithm where every state has *b* successors, the root of the tree generates *b* nodes at the first level, each of which generates *b* successors, for a total of *b<sup>2</sup>* at the second level. Each of these generates *b* more nodes, yielding *b<sup>3</sup>* nodes at the third level, and so on. Now suppose that the goal state is at depth *d*. In the worst case, it is the last node generated at that level. Then the total number of nodes generated is *b + b<sup>2</sup> + b<sup>3</sup> + ... + b<sup>d</sup> = O(b<sup>d</sup>)*. This complexity applies to both time and space, because we have to store all the previously checked nodes. This makes BSF unpractical, especially regarding memory (one might wait 13 days for a solution, but probably doesn’t have a computer with 1 petabyte of memory).

## Uniform-cost search
We can extend BFS to work with a graph where the edges can have different positive weights, using the **uniform-cost search (UCS)**. Instead of checking the next child in line, we pick the node with the lowest path cost (the path cost of a node is the sum of the weights from the initial node to that node). A convenient way to get the next node to be checked is using a **priority queue** as the frontier.

In contrast to BFS, because the weights are different, we can’t assume we hold the best path to a node when we insert it into the frontier. Maybe there is a better path that has not been found yet. Accordingly, in UCS we always check if the transition from the current node to each of its children yields a shorter path, even if that child node had been already put on the frontier. We are only sure we have the best path to a node when we pick that node from the frontier. As a consequence, the best path to the goal state is only found when we pop the goal state from the frontier.

Because of all those extra checks, UCS may perform much worse than BFS, rending its use unfeasible in practical settings.

## Depth-first search
If we use a **last-in-first-out (LIFO) queue** instead of a FIFO one as our frontier, we have **depth-first search (DFS)**. DFS is neither a complete algorithm (it may get stuck) or optimal. But its space complexity is much lower than BFS and UCS because every time you reach a node with no unvisited children, you can eliminated that node from the memory.

We can then combine DFS with the **iterative deepening** technique: search with depth 1, then 2, then 3, and so on until the goal is found. This adjustment makes an algorithm that is complete, optimal and requires less memory—*O(bd)* if all the weighs are the same.

## Greedy best-first search
We can add a **heuristic function** to better inform our search algorithm, hoping to check less nodes in our way to the goal. This heuristic function should return an educated guess of the path cost from an arbitrary node to the goal. Think of it as the distance of a given state to the goal state. In fact, the traditional Euclidian distance usually makes for a good heuristic function.

The **greedy best-first search** uses such a heuristic to choose the next node to check. As in the UCS, a priority queue should be used.

Greedy best-first search is not optimal, because it may follow a path composed of many short sub-paths, while an immediate longer but with a total lower cost path may exist.

## A* search

**A-star (A\*) search** takes the path cost of a node into consideration along with a heuristic function. In this search, the next node in line to be checked is the one with the smaller **sum of path cost and heuristic function**. It can be seen as a mixture of UCS and greedy best-first search.

If the heuristic is **admissible**, A* is guaranteed to find the optimal solution. An admissible heuristic is one that never overestimates the cost to reach the goal. Another way to put this is that an admissible heuristic function is always optimistic.

The use of A* with a good heuristic provides enormous savings in computation time compared to the use of an uninformed search. However, space complexity is still an issue. To this day, feasible algorithms for large-scale graphs are only possible with the sacrifice of the guaranty of optimality.