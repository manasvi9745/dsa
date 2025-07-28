# 🚀 Shortest Distance in Undirected, Unweighted Graph using BFS

---

## 🔴 Problem Statement

Given an undirected, unweighted graph with `n` nodes and a source node `src`, find the **minimum number of edges** required to reach every other node from the source.

---

## 🧠 Why BFS is Used (and Not DFS)?

### 📌 Reason:

- BFS (Breadth-First Search) works **level-by-level**.
- In **unweighted graphs**, the **first time** you reach a node is guaranteed to be the **shortest path** to it.
- DFS goes deep — it might go through **longer paths first**, so it **cannot guarantee shortest distance** without backtracking.

✅ **So, BFS is best suited for shortest path in unweighted graphs.**

---

## 🛠️ Variables and Their Roles

| Variable     | Type           | Purpose                                                         |
| ------------ | -------------- | --------------------------------------------------------------- |
| `visited[]`  | `vector<bool>` | To mark visited nodes (prevents loops and redundant processing) |
| `dist[]`     | `vector<int>`  | Stores shortest distance from `src` to each node                |
| `queue<int>` | `queue`        | Required by BFS to process nodes in FIFO order (level-by-level) |

---

## ✅ Approach (Easy Explanation)

We are given an **undirected** graph where each edge has a **unit weight (1)**. We need to find the **shortest path from the source node to all other nodes**.

We use **BFS**, because it traverses level-by-level, and the **first time** we reach a node, we’ve found its shortest path.

---

## 🧩 Code: Basic BFS using `queue<int>`

```cpp
class Solution {
public:
    vector<int> shortestPath(vector<vector<int>>& adj, int src) {
        int n = adj.size();

        // 1. Convert edge list to adjacency list
        vector<vector<int>> list(n);
        for(auto i : adj){
            int u = i[0];
            int v = i[1];
            list[u].push_back(v);
            list[v].push_back(u);
        }

        // 2. Create visited[] and distance[] arrays
        vector<int> visited(n, 0);
        vector<int> distance(n, -1); // -1 means not reachable initially

        // 3. Start BFS from the source node
        queue<int> q;
        distance[src] = 0;
        visited[src] = 1;
        q.push(src);

        // 4. Perform BFS
        while(!q.empty()){
            int node = q.front();
            q.pop();

            for(auto neighbor : list[node]){
                if(!visited[neighbor]){
                    q.push(neighbor);
                    distance[neighbor] = distance[node] + 1;
                    visited[neighbor] = 1;
                }
            }
        }

        // 5. Return the final distance array
        return distance;
    }
};
```

---

## 🔍 Step-by-Step Breakdown

### 1. Edge List to Adjacency List

`adj[i] = [u, v]`\
We add `v` to `u`'s list and `u` to `v`'s list (since it's an undirected graph).

### 2. Arrays Used

- `visited[n]`: Tracks visited nodes to avoid cycles
- `distance[n]`: Stores the shortest path from `src` to each node (initialized to -1)

### 3. BFS Initialization

```cpp
distance[src] = 0;
visited[src] = 1;
queue.push(src);
```

### 4. BFS Traversal

For every node:

- Pop from queue
- Visit unvisited neighbors
- Set `distance = current + 1`
- Mark neighbor as visited

### 5. Final Output

Return `distance[]` array.\
Unreachable nodes will still have `-1`.

---

## 🧪 Dry Run Table

Given:

- Edges: `[[0,1], [0,3], [1,2], [3,4]]`
- Source Node: `0`
- Total Nodes: `5`

### Adjacency List:

```
list[0] = [1, 3]
list[1] = [0, 2]
list[2] = [1]
list[3] = [0, 4]
list[4] = [3]
```

### BFS Step-by-Step:

| Step | Queue  | Current Node | Neighbors | visited[]       | distance[]          |
| ---- | ------ | ------------ | --------- | --------------- | ------------------- |
| 0    | [0]    | -            | -         | [1, 0, 0, 0, 0] | [0, -1, -1, -1, -1] |
| 1    | [1, 3] | 0            | 1, 3      | [1, 1, 0, 1, 0] | [0, 1, -1, 1, -1]   |
| 2    | [3, 2] | 1            | 0, 2      | [1, 1, 1, 1, 0] | [0, 1, 2, 1, -1]    |
| 3    | [2, 4] | 3            | 0, 4      | [1, 1, 1, 1, 1] | [0, 1, 2, 1, 2]     |
| 4    | [4]    | 2            | 1         | [1, 1, 1, 1, 1] | [0, 1, 2, 1, 2]     |
| 5    | []     | 4            | 3         | [1, 1, 1, 1, 1] | [0, 1, 2, 1, 2]     |

✅ Final Output:

```cpp
distance[] = [0, 1, 2, 1, 2]
```

---

## 💡 Alternative: Pair-Based BFS

### 🧠 Concept:

Use `queue<pair<int, int>>` to store `{node, distance}` directly in the queue.

### ✅ Code:

```cpp
class Solution {
public:
    vector<int> shortestPath(vector<vector<int>>& adj, int src) {
        int n = adj.size();

        // Step 1: Build Adjacency List
        vector<vector<int>> list(n);
        for(auto edge : adj){
            int u = edge[0], v = edge[1];
            list[u].push_back(v);
            list[v].push_back(u);
        }

        // Step 2: Initialize
        vector<int> visited(n, 0);
        vector<int> distance(n, -1);
        queue<pair<int, int>> q;

        // Step 3: Start BFS
        q.push({src, 0});
        visited[src] = 1;
        distance[src] = 0;

        // Step 4: BFS Loop
        while(!q.empty()){
            auto [node, dist] = q.front();
            q.pop();

            for(int neighbor : list[node]){
                if(!visited[neighbor]){
                    visited[neighbor] = 1;
                    distance[neighbor] = dist + 1;
                    q.push({neighbor, dist + 1});
                }
            }
        }

        return distance;
    }
};
```

---

## ❗ Common Mistakes

- **Not using **``: Leads to revisiting nodes and incorrect distances.
- **Using **``** without storing in **``: Breaks logic if a node is re-added with smaller distance later.

---

## 💬 What to Say in Interviews

> "I’m using BFS because in an unweighted graph, BFS naturally finds the shortest path in terms of edge count."

> "I maintain a `visited[]` to ensure we don’t revisit nodes and avoid infinite loops."

> "The `dist[]` array allows us to store the shortest path length from the source node."

> "If the graph was weighted, I would use Dijkstra’s Algorithm instead."

---

## 📊 Time and Space Complexity

- **Time Complexity**: `O(N + E)`\
  Where `N` = number of nodes, `E` = number of edges

- **Space Complexity**: `O(N)`\
  For `visited[]`, `distance[]`, queue, and adjacency list

