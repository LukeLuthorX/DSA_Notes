#DSA #graphs
```c
#include <stdio.h>

#define INF 100 // Use 100 to represent "no connection"

int main() {
    int cost[6][6], distance[6], visited[6];
    int n, source;

    // Step 1: Get the number of nodes (routers)
    printf("Enter the number of routers (max 6): ");
    scanf("%d", &n);

    if (n > 6) {
        printf("The number of routers cannot exceed 6.\n");
        return 1; // Exit the program if the user inputs more than allowed nodes
    }

    // Step 2: Get the cost matrix
    printf("Enter the cost matrix (use 100 for no connection):\n");
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            scanf("%d", &cost[i][j]);
        }
    }

    // Step 3: Get the source router
    printf("Enter the source router (0 to %d): ", n - 1);
    scanf("%d", &source);

    // Step 4: Initialize distances and visited array
    for (int i = 0; i < n; i++) {
        distance[i] = cost[source][i]; // Distance to each router from the source
        visited[i] = 0;               // No router is visited initially
    }
    visited[source] = 1; // Mark source router as visited

    // Step 5: Main Dijkstra's algorithm loop
    for (int step = 1; step < n; step++) {
        int minDistance = INF, nearestRouter = -1;

        // Find the nearest unvisited router
        for (int i = 0; i < n; i++) {
            if (!visited[i] && distance[i] < minDistance) {
                minDistance = distance[i];
                nearestRouter = i;
            }
        }

        // Mark the nearest router as visited
        visited[nearestRouter] = 1;

        // Update the distances for neighbors of the nearest router
        for (int i = 0; i < n; i++) {
            if (!visited[i] && cost[nearestRouter][i] != INF) {
                if (distance[nearestRouter] + cost[nearestRouter][i] < distance[i]) {
                    distance[i] = distance[nearestRouter] + cost[nearestRouter][i];
                }
            }
        }
    }

    // Step 6: Print the shortest distances
    printf("Shortest distances from router %d:\n", source);
    for (int i = 0; i < n; i++) {
        if (distance[i] == INF) {
            printf("Router %d: No path\n", i);
        } else {
            printf("Router %d: %d\n", i, distance[i]);
        }
    }

    return 0;
}

```