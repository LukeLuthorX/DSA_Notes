#DSA #graphs

---

## DFS
```c
#include <stdio.h>

void dfs(int graph[5][5], int visited[], int node) {
    // Mark the current node as visited
    visited[node] = 1;
    printf("%d ", node);  // Print the visited node

    // Recur for all adjacent nodes
    for (int i = 0; i < 5; i++) {
        if (graph[node][i] == 1 && !visited[i]) {  // If there's an edge and it's unvisited
            dfs(graph, visited, i);
        }
    }
}

int main() {
    // Define an example adjacency matrix
    int graph[5][5] = {
        {0, 1, 1, 0, 0},
        {1, 0, 1, 1, 0},
        {1, 1, 0, 0, 1},
        {0, 1, 0, 0, 1},
        {0, 0, 1, 1, 0}
    };

    int visited[5] = {0};  // Initialize all nodes as unvisited

    printf("DFS traversal starting from node 0:\n");
    dfs(graph, visited, 0, 5);  // Start DFS from node 0

    return 0;
}

```

## BFS
```c
#include <stdio.h>

void bfs(int graph[5][5], int start_node) {
    int visited[5] = {0};  // Mark all nodes as unvisited
    int queue[5], front = 0, rear = -1;  // Queue for BFS

    // Start BFS from the given node
    visited[start_node] = 1;
    queue[++rear] = start_node;

    while (front <= rear) {
        int node = queue[front++];  // Dequeue the next node
        printf("%d ", node);

        // Enqueue all unvisited neighbors of the dequeued node
        for (int i = 0; i < num_nodes; i++) {
            if (graph[node][i] == 1 && !visited[i]) {
                visited[i] = 1;
                queue[rear++] = i;
            }
        }
    }
}

int main() {
    // Define an example adjacency matrix
    int graph[MAX_NODES][MAX_NODES] = {
        {0, 1, 1, 0, 0},
        {1, 0, 1, 1, 0},
        {1, 1, 0, 0, 1},
        {0, 1, 0, 0, 1},
        {0, 0, 1, 1, 0}
    };

    printf("BFS traversal starting from node 0:\n");
    bfs(graph, 0);  // Start BFS from node 0

    return 0;
}

```


>[!NOTE]
>The mutli-dimensional array has to have initialized size before passing into function so cant use 'n' received from the user, define it as max, 100


## Dijkstra 
```c
#include <stdio.h>  
#include <limits.h>  

#define MAX_VERTICES 100  

int main() {  
    int n, m;  
    int graph[MAX_VERTICES][MAX_VERTICES];  
    int dist[MAX_VERTICES], prev[MAX_VERTICES];  
    int visited[MAX_VERTICES];  

    // Initialize the graph with a large number (infinity)  
    for (int i = 0; i < MAX_VERTICES; i++) {  
        for (int j = 0; j < MAX_VERTICES; j++) {  
            graph[i][j] = (i == j) ? 0 : INT_MAX;  
        }  
    }  

    // Input number of vertices and edges  
    printf("Enter the number of vertices: ");  
    scanf("%d", &n);  
    printf("Enter the number of edges: ");  
    scanf("%d", &m);  

    // Input edges and weights  
    printf("Enter the edges (u, v, w):\n");  
    for (int i = 0; i < m; i++) {  
        int u, v, w;  
        scanf("%d %d %d", &u, &v, &w);  
        graph[u][v] = w; // directed edge  
        graph[v][u] = w; // undirected edge (if needed, otherwise remove this line)  
    }  

    // Input source and destination vertices  
    int s, d;  
    printf("Enter the source vertex: ");  
    scanf("%d", &s);  
    printf("Enter the destination vertex: ");  
    scanf("%d", &d);  

    // Initialize distances, previous nodes, and visited array  
    for (int i = 0; i < n; i++) {  
        dist[i] = INT_MAX;  
        prev[i] = -1;  
        visited[i] = 0;  
    }  
    dist[s] = 0; // Distance to the source is 0  

    // Main loop of Dijkstra's algorithm  
    for (int i = 0; i < n - 1; i++) {  
        int minDist = INT_MAX;  
        int u = -1;  

        // Find the unvisited vertex with the smallest distance  
        for (int j = 0; j < n; j++) {  
            if (!visited[j] && dist[j] < minDist) {  
                minDist = dist[j];  
                u = j;  
            }  
        }  

        if (u == -1) break; // No more reachable vertices  
        visited[u] = 1;  

        // Update distances to neighbors  
        for (int v = 0; v < n; v++) {  
            if (graph[u][v] != INT_MAX && !visited[v]) { // Check if edge exists  
                if (dist[u] + graph[u][v] < dist[v]) {  
                    dist[v] = dist[u] + graph[u][v];  
                    prev[v] = u; // Track the path  
                }  
            }  
        }  
    }  

    // Output the result  
    if (dist[d] == INT_MAX) {  
        printf("No path found\n");  
    } else {  
        printf("Shortest path: ");  
        int path[MAX_VERTICES];  
        int pathIndex = 0;  
        for (int v = d; v != -1; v = prev[v]) {  
            path[pathIndex++] = v;  
        }  
        for (int i = pathIndex - 1; i >= 0; i--) {  
            printf("%d", path[i]);  
            if (i > 0) printf(" -> ");  
        }  
        printf("\nShortest distance: %d\n", dist[d]);  
    }  

    return 0;  
}
```

## Prim's Algorithm
```c
#include <stdio.h>
#include <limits.h>

#define V 5  // Number of vertices in the graph

// Function to find the vertex with the minimum key value, from the set of vertices not yet included in MST
int minKey(int key[], int mstSet[]) {
    int min = INT_MAX, min_index;
    for (int v = 0; v < V; v++)
        if (mstSet[v] == 0 && key[v] < min) {
            min = key[v];
            min_index = v;
        }
    return min_index;
}

// Function to print the constructed MST stored in parent[]
void printMST(int parent[], int graph[V][V]) {
    printf("Edge \tWeight\n");
    for (int i = 1; i < V; i++)
        printf("%d - %d \t%d \n", parent[i], i, graph[i][parent[i]]);
}

// Function to construct and print MST for a graph represented using adjacency matrix representation
void primMST(int graph[V][V]) {
    int parent[V];    // Array to store constructed MST
    int key[V];       // Key values used to pick minimum weight edge in cut
    int mstSet[V];    // To represent set of vertices included in MST

    // Initialize all keys as INFINITE
    for (int i = 0; i < V; i++) {
        key[i] = INT_MAX;
        mstSet[i] = 0;
    }

    // Always include first 1st vertex in MST.
    key[0] = 0;        // Make key 0 so that this vertex is picked as first vertex
    parent[0] = -1;    // First node is always root of MST

    // The MST will have V vertices
    for (int count = 0; count < V - 1; count++) {
        // Pick the minimum key vertex from the set of vertices not yet included in MST
        int u = minKey(key, mstSet);

        // Add the picked vertex to the MST Set
        mstSet[u] = 1;

        // Update key value and parent index of the adjacent vertices of the picked vertex.
        for (int v = 0; v < V; v++)
            // graph[u][v] is non zero only for adjacent vertices of u
            // mstSet[v] is false for vertices not yet included in MST
            // Update the key only if graph[u][v] is smaller than key[v]
            if (graph[u][v] && mstSet[v] == 0 && graph[u][v] < key[v]) {
                parent[v] = u;
                key[v] = graph[u][v];
            }
    }

    // print the constructed MST
    printMST(parent, graph);
}

int main() {
    // Example adjacency matrix representation of the graph
    int graph[V][V] = {
        {0, 2, 0, 6, 0},
        {2, 0, 3, 8, 5},
        {0, 3, 0, 0, 7},
        {6, 8, 0, 0, 9},
        {0, 5, 7, 9, 0}
    };

    // Print the solution
    primMST(graph);

    return 0;
}

```

## Kruskal's Algorithm
```c
#include <stdio.h>
#include <limits.h>

#define V 5  // Number of vertices in the graph
#define E 10 // Maximum number of edges (complete graph)

// Find function for union-find
int find(int parent[], int i) {
    if (parent[i] == i)
        return i;
    return find(parent, parent[i]);
}

// Union function for union-find
void unionSet(int parent[], int rank[], int x, int y) {
    int xroot = find(parent, x);
    int yroot = find(parent, y);

    // Attach smaller rank tree under root of high rank tree
    if (rank[xroot] < rank[yroot])
        parent[xroot] = yroot;
    else if (rank[xroot] > rank[yroot])
        parent[yroot] = xroot;
    else {
        parent[yroot] = xroot;
        rank[xroot]++;
    }
}

// Function to sort edges by weight
void sortEdges(int edges[E][3], int edgeCount) {
    for (int i = 0; i < edgeCount - 1; i++) {
        for (int j = 0; j < edgeCount - i - 1; j++) {
            if (edges[j][2] > edges[j + 1][2]) {
                int temp0 = edges[j][0], temp1 = edges[j][1], temp2 = edges[j][2];
                edges[j][0] = edges[j + 1][0];
                edges[j][1] = edges[j + 1][1];
                edges[j][2] = edges[j + 1][2];
                edges[j + 1][0] = temp0;
                edges[j + 1][1] = temp1;
                edges[j + 1][2] = temp2;
            }
        }
    }
}

int main() {
    // Example adjacency matrix representation of the graph
    int graph[V][V] = {
        {0, 2, 0, 6, 0},
        {2, 0, 3, 8, 5},
        {0, 3, 0, 0, 7},
        {6, 8, 0, 0, 9},
        {0, 5, 7, 9, 0}
    };

    int edges[E][3];  // Array to store edges (u, v, weight)
    int edgeCount = 0;

    // Convert adjacency matrix to edge list
    for (int i = 0; i < V; i++) {
        for (int j = i + 1; j < V; j++) {
            if (graph[i][j] != 0) {
                edges[edgeCount][0] = i;
                edges[edgeCount][1] = j;
                edges[edgeCount][2] = graph[i][j];
                edgeCount++;
            }
        }
    }

    // Sort all edges by weight
    sortEdges(edges, edgeCount);

    int parent[V];
    int rank[V];

    // Initialize union-find structure
    for (int i = 0; i < V; i++) {
        parent[i] = i;
        rank[i] = 0;
    }

    printf("Edge \tWeight\n");
    int mstEdges = 0, i = 0;

    // Process edges in sorted order
    while (mstEdges < V - 1 && i < edgeCount) {
        int u = edges[i][0];
        int v = edges[i][1];
        int weight = edges[i][2];
        i++;

        // Find the root of the sets of u and v
        int set_u = find(parent, u);
        int set_v = find(parent, v);

        // If including this edge doesn't cause a cycle
        if (set_u != set_v) {
            printf("%d - %d \t%d\n", u, v, weight);
            unionSet(parent, rank, set_u, set_v);
            mstEdges++;
        }
    }

    return 0;
}

```

---
### Prims
```c
// Function to implement Prim's Algorithm
void primsAlgorithm(int graph[N][N], int N) {
    int parent[N];    // To store the MST (parent of each node in MST)
    int key[N];       // To store the minimum weights to connect to the MST
    int inMST[N];     // To check if the vertex is already in MST

    // Step 1: Initialize all keys to infinity and inMST to false
    for (int i = 0; i < N; i++) {
        key[i] = INT_MAX;
        inMST[i] = 0;
    }

    // Step 2: Start from the first vertex
    key[0] = 0;         // Start from vertex 0
    parent[0] = -1;     // The root node of the MST

    // Step 3: Build the MST (N vertices, so N-1 edges)
    for (int count = 0; count < N - 1; count++) {
        // Find the vertex with the minimum key value not in MST
        int minKey = INT_MAX, u;

        for (int v = 0; v < N; v++) {
            if (!inMST[v] && key[v] < minKey) {
                minKey = key[v];
                u = v;
            }
        }

        // Add the selected vertex to the MST
        inMST[u] = 1;

        // Update key values and parent index for adjacent vertices of the picked vertex
        for (int v = 0; v < N; v++) {
            // Update key[v] only if graph[u][v] is smaller and v is not in MST
            if (graph[u][v] && !inMST[v] && graph[u][v] < key[v]) {
                key[v] = graph[u][v];
                parent[v] = u;
            }
        }
    }

    // Step 4: Print the MST
    printf("Edge \tWeight\n");
    for (int i = 1; i < N; i++) {
        printf("%d - %d \t%d\n", parent[i], i, graph[i][parent[i]]);
    }
}

```

[[Bellman-Ford - C]]

[[Dijksta - CN]]
