#DSA #graphs
```c
#include <stdio.h>
#define INF 99999
#define MAX_VERTICES 20

void bellman_ford(int n, int graph[MAX_VERTICES][MAX_VERTICES], int src) {
    int dist[MAX_VERTICES];
    for (int i = 0; i < n; i++)
        dist[i] = INF;
    dist[src] = 0;

    // Relax edges repeatedly
    for (int i = 0; i < n - 1; i++) {
        for (int u = 0; u < n; u++) {
            for (int v = 0; v < n; v++) {
                if (graph[u][v] != INF && dist[u] + graph[u][v] < dist[v]) {
                    dist[v] = dist[u] + graph[u][v];
                }
            }
        }
    }
	
    printf("Distances from node %d:\n", src);
    for (int i = 0; i < n; i++) {
        printf("To %d: %d\n", i, dist[i]);
    }
}

int main() {
    int n, graph[MAX_VERTICES][MAX_VERTICES];
    scanf("%d", &n);
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            scanf("%d", &graph[i][j]);
            // Initialize INF for unreachable edges
            if (graph[i][j] == 0 && i != j) {
                graph[i][j] = INF;
            }
        }
    }
    for (int i = 0; i < n; i++) {
        bellman_ford(n, graph, i);
        printf("\n");
    }
    return 0;
}
```
---
