[[Graphs]]
##### Shortest Path in Undirected Graph (unit weight)
link - [shortest path problem](https://www.geeksforgeeks.org/problems/shortest-path-in-undirected-graph-having-unit-distance/1)

Standard BFS with a greedy approach worked here since the nodes were already arranged and sorted at same distance
```cpp
class Solution {
  public:
    vector<int> shortestPath(vector<vector<int>>& adj, int src) {
        int V = adj.size();
        vector<int>distance(V);
        for(int i = 0;i<V;i++) distance[i] = 1e9; //initialize distance to inf
        
        distance[src] = 0; //node src is at distance 0 to itself
        queue<int>q;
        q.push(src);
        while(!q.empty()){
            int node = q.front();
            q.pop();
            for(auto it: adj[node]){
                //its neigbours are 1 distance apart from where
                //this node is and is this path smaller, then update
                if(distance[node] + 1 < distance[it]){
                    distance[it] = distance[node] + 1;
                    q.push(it);
                }
            }
        }
        vector<int>ans(V,-1);
        for(int i = 0;i<V;i++){
            if(distance[i] != 1e9) ans[i] = distance[i];
        }
        return ans;
    }
};
```

---
##### Shortest Path in a DAG
link - [problem](https://www.geeksforgeeks.org/problems/shortest-path-in-undirected-graph/1)

did topo sort in this problem because the node we start with, there is no node before it, and once done for that node, next node has nothing behind it left, all previous nodes are already computed and that particular node can be reached only from previous nodes since DAG, nodes coming forward can't reach this node.
eg. 6 5 4 2, from 6, 5 can be visited, then from 5, 4 can be visited, but no next node can help to reach previous node, so we go sequentially and the approach is optimized.
Djikstra's min-heap need is gone because of this and problem is solved with less time
```cpp
// User function Template for C++
class Solution {
  public:
    void topoSort(int node, vector<int>&visited, stack<int>&st, vector<vector<pair<int,int>>>&adj){
        //DFS approach of Topo Sort
        visited[node] = 1;
        for(auto it:adj[node]){
            int v = it.first;
            if(!visited[v]){
                topoSort(v,visited,st,adj);
            }
        }
        st.push(node);
    }
    vector<int> shortestPath(int V, int E, vector<vector<int>>& edges) {
        //apply topo sort and then in that order perform relaxation
        //we store adj list with weights as vector of vector of pairs
        vector<vector<pair<int,int>>>adj(V);
        for(auto it: edges){
            int u = it[0];
            int v = it[1];
            int wt = it[2];
            adj[u].push_back({v,wt});
        }
        
        //topo sort
        vector<int>visited(V);
        stack<int>st;
        for(int i = 0; i<V; i++){
            if(!visited[i]){
                topoSort(i,visited,st,adj);
            }
        }
        
        //relaxation
        vector<int>distance(V);
        for(int i = 0;i<V;i++) distance[i] = 1e9;
        distance[0] = 0;
        
        while(!st.empty()){
            int node = st.top();
            st.pop();
            
            for(auto it:adj[node]){
                int v = it.first;
                int wt = it.second;
                
                if(distance[node] + wt < distance[v]){
                    distance[v] = distance[node]+wt;
                }
            }
        }
        vector<int> ans(V,-1);
        for(int i = 0;i<V;i++){
            if(distance[i] != 1e9) ans[i] = distance[i];
        }
        
        return ans;
        
    }
};

```

---
##### Dijkstra's Algorithm

> [!NOTE]- min-heap
> min-heap (priority queue) -> the smaller value is at top
> ![[Pasted image 20250626001741.png|400]]
> `priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;`
> meaning->
> - `pair<int,int>` is the type which pq is storing. {distance, node}
> - `vector<pair<int,int>>` -> container internally used for storing this pair
> - `greater<pair<int,int>>` -> comparator
> _smallest_ first value in its `pair` (the distance) will always be at the front.

> [!NOTE]- why works only for non-negative
> works only for non-negative weights -> lets say there are 2 nodes 0 and 1 and weight between them is -2, so distance to reach 1 from 0 node is -2 (<inf), so now reaches node 1, now going from node 1 to 0. distance is -2 + -2 = -4, so it goes back to 0, and again -4+-2 = -6. 0 comes to 1 node and this continues and *gets stuck in infinite loop*

> [!NOTE]- Intuition
> The intuition is if you reach a node with shortest path (starts with source, since inf -> 0) then push it to the queue and dequeue and traverse all the nodes it can visit and if the visited node got the distance decreased (or initialized from inf to some value), put it into the queue, if the distance did not lower, do not put in queue. Now dequeue the shortest distance node from queue and traverse its neighbours performing relaxation. 
> Even queue works with this but takes more iteration and priority queue is faster since it already gives the shortest distance from the queue.
> 
> Take reference to this graph theory method of how we did it theoretically:
> ![[Pasted image 20250626011133.png|600]]

```cpp
class Solution {
  public:
    vector<int> shortestPath(int n, int m, vector<vector<int>>& edges) {
        n++;
        vector<vector<pair<int,int>>>adj(n);
        for(auto it:edges){
            adj[it[0]].push_back({it[1],it[2]});
            adj[it[1]].push_back({it[0],it[2]});
        }
        
        //min-heap to get smallest distance pair
        priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;
        
        vector<int>dist(n);
        for(int i = 0;i<n;i++) dist[i] = 1e9; //initialize dist to inf
        
        //pq -> {distance, node}
        dist[0] = 0;
        pq.push({0,0});
        
        while(!pq.empty()){
            int dis = pq.top().first;
            int node = pq.top().second;
            pq.pop();
            for(auto it:adj[node]){
                int adjNode = it.first;
                int edgeWeight = it.second;
                
                if(dis + edgeWeight < dist[adjNode]){
                    dist[adjNode] = dis + edgeWeight;
                    pq.push({dist[adjNode],adjNode});
                }
            }
        }
        
        return dist;
        
    }
};
```

T.C = $E \times log(V)$

###### Using Set (more optimized)

> [!NOTE]- why set
> set stores in ascending order (unique values), so the top value has smallest.
> 
> lets say node 5 was visited at distance 10, {10,5} is present in set, but in another iteration you reached node 5 at distance 8, now {8,5} goes to set, but {10,5} is not needed, it will not give the shortest path and increase computation, in set we can erase it which cannot be done in priority_queue. Although set.erase takes logN time, so it doesnt decrease time complexity significantly

```cpp
// User Function Template
class Solution {
  public:
    vector<int> dijkstra(int V, vector<vector<int>> &edges, int src) {
        vector<vector<pair<int,int>>> adj(V);
        for(auto it:edges){
            int u = it[0];
            int v = it[1];
            int w = it[2];
            adj[u].push_back({v,w});
        }
        
        vector<int>dist(V,1e9);
        
        set<pair<int,int>>st;
        
        dist[src] = 0;
        st.insert({0,src});
        
        while(!st.empty()){
            auto it = *(st.begin()); //smallest value at beginning of set
            int dis = it.first;
            int node = it.second;
            st.erase(it);
            for(auto itr: adj[node]){
                int adjNode = itr.first;
                int wt = itr.second;
                if(dis + wt < dist[adjNode]){
                    //erase if it distance already exists (some node already visited it)
                    //but we have found smaller so removing it from set so that this pair
                    // is not needed and will only cause computation with no optimization 
                    // in distance
                    if(dist[adjNode] != 1e9)
                        st.erase({dist[adjNode],adjNode});
                        
                    dist[adjNode] = dis + wt;
                    st.insert({dist[adjNode],adjNode});
                }
            }
        }
        return dist;
    }
};
```

---
##### Shortest Path (Dijkstra)
link - [Shortest Path in Weighted undirected graph](https://www.geeksforgeeks.org/problems/shortest-path-in-weighted-undirected-graph/1)

![[Pasted image 20250626024529.png|400]]

```cpp
class Solution {
  public:
    vector<int> shortestPath(int n, int m, vector<vector<int>>& edges) {
        vector<vector<pair<int,int>>>adj(n+1);
        for(auto it:edges){
            int u = it[0];
            int v = it[1];
            int w = it[2];
            adj[u].push_back({v,w});
            adj[v].push_back({u,w});
        }
        priority_queue<pair<int,int>,vector<pair<int,int>>, greater<pair<int,int>>>pq;
        
        vector<int>dist(n+1,1e9);
        vector<int>parent(n+1); //memoization array to store 
                              //where node came from
        for(int i = 1;i<=n;i++){
            parent[i] = i; //initially each node is on itself, no path
        }
        
        dist[1] = 0;
        pq.push({0,1});
        
        while(!pq.empty()){
            auto it = pq.top();
            pq.pop();
            int dis = it.first;
            int node = it.second;
            
            for(auto itr: adj[node]){
                int adjNode = itr.first;
                int wt = itr.second;
                if(dis + wt < dist[adjNode]){
                    dist[adjNode] = dis + wt;
                    pq.push({dist[adjNode],adjNode});
                    parent[adjNode] = node;
                }
            }
        }
        if(dist[n] == 1e9)return {-1}; //unreachable
        vector<int>path;
        int node = n;
        while(parent[node]!=node){ //will stop at node 1, whose parent is 1 itself
            path.push_back(node);
            node = parent[node];
        }
        path.push_back(1);
        reverse(path.begin(),path.end());
        path.insert(path.begin(),dist[n]);
        return path;
        
    }
};
```

---
##### Shortest Path in Binary Matrix
link - [Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/)
```cpp
class Solution {
public:
    int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
        int n = grid.size();
        vector<vector<int>> dist(n, vector<int>(n, 1e9));
        dist[0][0] = 1;
        // priority_queue not needed as distance increases uniformly (by 1)
        queue<pair<int, pair<int, int>>> q; // {distance, {row, col}}
        q.push({1, {0, 0}});
        if (grid[0][0] == 1) return -1;

        while (!q.empty()) {
            auto it = q.front();
            q.pop();
            int dis = it.first;
            int row = it.second.first;
            int col = it.second.second;

            for (int i = -1; i <= 1; i++) {
                for (int j = -1; j <= 1; j++) {
                    int nrow = row + i;
                    int ncol = col + j;
                    if (nrow >= 0 && nrow < n && ncol >= 0 && ncol < n && grid[nrow][ncol] == 0 && dis + 1 < dist[nrow][ncol]) {
                        dist[nrow][ncol] = dis + 1;
                        q.push({dist[nrow][ncol], {nrow, ncol}});
                    }
                }
            }
        }

        if (dist[n-1][n-1] == 1e9) return -1;
        else return dist[n-1][n-1];
    }
};
```

---
##### Path With Minimum Effort
link - [Path With Minimum Effort](https://leetcode.com/problems/path-with-minimum-effort/)
```cpp
class Solution {
public:
    int minimumEffortPath(vector<vector<int>>& heights) {
        int n = heights.size();
        int m = heights[0].size();
        vector<vector<int>> effort(n, vector<int>(m, 1e9));
        // {absolute diff, {row, col}}
        priority_queue<pair<int, pair<int, int>>, vector<pair<int, pair<int, int>>>, greater<pair<int, pair<int, int>>>> pq;
        effort[0][0] = 0; // it's standing on that height already
        pq.push({0, {0, 0}});

        while (!pq.empty()) {
            auto it = pq.top();
            pq.pop();
            int dis = it.first;
            int row = it.second.first;
            int col = it.second.second;

            int delr[] = {-1, 0, 1, 0};
            int delc[] = {0, -1, 0, 1};
            for (int i = 0; i < 4; i++) {
                int nrow = row + delr[i];
                int ncol = col + delc[i];
                if (nrow >= 0 && nrow < n && ncol >= 0 && ncol < m && max(dis, abs(heights[row][col] - heights[nrow][ncol])) < effort[nrow][ncol]) {
                    effort[nrow][ncol] = max(dis, abs(heights[row][col] - heights[nrow][ncol]));
                    pq.push({effort[nrow][ncol], {nrow, ncol}});
                }
            }
        }

        return effort[n-1][m-1];
    }
};
```

---
##### Cheapest Flights Within K Stops
link - [Cheapest Flights Within K Stops](https://leetcode.com/problems/cheapest-flights-within-k-stops/)
```cpp
class Solution {
public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        vector<vector<pair<int, int>>> adj(n);
        for (auto it : flights) {
            int u = it[0];
            int v = it[1];
            int price = it[2];
            adj[u].push_back({v, price});
        }

        // {stops, {node, price}}
        // since stops increase by +1, no need of priority queue
        // even if reach the destination, do not stop, may reach it in lesser price in
        // next steps (since stops was taken as first judgement, not price)
        queue<pair<int, pair<int, int>>> q;
        q.push({0, {src, 0}});
        vector<int> dist(n, 1e9);
        dist[src] = 0;

        while (!q.empty()) {
            auto it = q.front();
            q.pop();
            int stops = it.first;
            int node = it.second.first;
            int price = it.second.second;

            // reaching the destination node doesn't count as stop
            if (stops > k) continue;

            for (auto itr : adj[node]) {
                int adjNode = itr.first;
                int edW = itr.second;
                if (price + edW < dist[adjNode] && stops <= k) {
                    dist[adjNode] = price + edW;
                    q.push({stops + 1, {adjNode, dist[adjNode]}});
                }
            }
        }

        if (dist[dst] == 1e9) return -1;
        return dist[dst];
    }
};
```
T.C = E logV -> but not using pq, so only E which is flights.size().
Therefore, T.C = E

---
##### Number of Ways to Arrive at Destination
link - [Number of Ways to Arrive at Destination](https://leetcode.com/problems/number-of-ways-to-arrive-at-destination/)
```cpp
class Solution {
public:
    int countPaths(int n, vector<vector<int>>& roads) {
        vector<vector<pair<int, int>>> adj(n);
        for (auto it : roads) {
            adj[it[0]].push_back({it[1], it[2]});
            adj[it[1]].push_back({it[0], it[2]});
        }

        priority_queue<pair<long long, int>, vector<pair<long long, int>>, greater<pair<long long, int>>> pq;
        pq.push({0, 0});
        vector<long long> dist(n, 1e18), ways(n, 0);
        dist[0] = 0;
        ways[0] = 1; // way to reach src is 1, since it's there already
        int mod = (int)(1e9 + 7);

        while (!pq.empty()) {
            auto it = pq.top();
            pq.pop();
            long long dis = it.first;
            int node = it.second;

            for (auto itr : adj[node]) {
                int adjNode = itr.first;
                int wt = itr.second;

                // visiting node with shortest path first time
                if (dis + wt < dist[adjNode]) {
                    dist[adjNode] = dis + wt;
                    pq.push({dist[adjNode], adjNode});
                    // we will reach the adjNode from the previous path
                    // so it will have the same number of ways
                    ways[adjNode] = ways[node] % mod;
                }
                // same node with shortest distance is visited again
                // this is from a different path, so this will be added
                // since new path, and since we have already traversed
                // the adjNode before (it had already been in queue for that distance)
                else if (dis + wt == dist[adjNode]) {
                    ways[adjNode] = (ways[node] + ways[adjNode]) % mod;
                }
            }
        }

        return ways[n - 1] % mod;
    }
};
```

---
##### Minimum Multiplications to reach End
link - [Minimum Multiplications to reach End](https://www.geeksforgeeks.org/problems/minimum-multiplications-to-reach-end/1)
Think of the multiplication result as nodes and distance as the steps and this boils down to a dijkstra's problem. *(the problem setter for this problem is Striver)*
![[Pasted image 20250628150228.png|400]]
```cpp
class Solution {
  public:
    int minimumMultiplications(vector<int>& arr, int start, int end) {
        if(start == end) return 0;
        //since steps are incremented by +1, we get the shortest at top
        //so pq is not needed as queue does the job
        //the nodes are from 0 to 9999, the distance is the steps to reach
        
        queue<pair<int,int>>q;
        q.push({start,0}); //{node, steps}
        vector<int>dist(100000,1e9);
        dist[start] = 0;
        //no need to iterate again if reached a node on same no. of steps
        int mod = 100000;
        while(!q.empty()){
            int node = q.front().first;
            int steps = q.front().second;
            q.pop();
            for(auto it:arr){
                int num = (it * node) % mod;
                if(steps + 1 < dist[num]){
                    dist[num] = steps + 1;
                    if(num == end) return steps + 1;
                    q.push({num, steps+1});
                }
            }
        }
        return -1;
    }
};
```

---
##### Bellman Ford Algorithm
link - [Bellman-Ford](https://www.geeksforgeeks.org/problems/distance-from-the-source-bellman-ford-algorithm/1)
Works on Directed Graph only, convert undirected to directed to apply this.

> [!NOTE]- Bellman Ford Explanation
> Djikstra algo had issue with negative weights and gave TLE for negative cycles. Bellman Ford Algorithm works well with negative edges and negative cycle. This operates on edges and these edges could be in any order. We perform `N-1` iterations of relaxation.
> 
> - Why `N-1` iterations of relaxation?
> >take example of a graph which is sequential: 1 -> 2 -> 3 -> 4.
> >Now if this graph is relaxed, first iteration would only relax the edge from 1->2, in 2nd iteration from 2->3 and so on, with entire graph completely relaxed in `N-1` iteration of relaxation of entire graph, so this is the worst case and in any graph this number cannot increase.
> 
> - How does it handle **Negative Cycle**?
> >If there was no stop of iterations then this cycle with all negative weights will go on and on, but since we have analyzed the worst case would only require `N-1` iterations of relaxation, any more relaxation decreasing the distance, we know its a negative cycle.

```cpp
class Solution {
  public:
    vector<int> bellmanFord(int V, vector<vector<int>>& edges, int src) {
        vector<int>dist(V,1e8);
        dist[src] = 0;
        //relaxation : V-1 times
        for(int i = 0;i<V-1;i++){
            for(auto it:edges){
                int u = it[0];
                int v = it[1];
                int wt = it[2];
                
                if(dist[u] != 1e8 && dist[u] + wt < dist[v]){
                    dist[v] = wt + dist[u];
                }
            }
        }
        
        //Nth iter to check negative cycle, since it will be 
        //keep decreasing even after N-1 iteration
        for(auto it:edges){
            int u = it[0];
            int v = it[1];
            int wt = it[2];
            if(dist[u] != 1e8 && dist[u] + wt < dist[v]) return {-1};
        }
        return dist;
    }
};
```

---
##### Floyd Warshall Algorithm
link - [Floyd Warshall Algorithm](https://www.geeksforgeeks.org/problems/implementing-floyd-warshall2042/1)

> [!NOTE]- Floyd Warshall Algorithm
> >Multisource shortest Path, can detect negative cycles as well.
> >Go via every node, it has a dynamic programming approach -> we are using something which is pre-computed
> - done on DG only, using adjacency matrix
> - `d[0][4]` -> we can reach from 0 to 4 directly, or 0 to 1 then 1 to 4, or 0 to 2, then 2 to 4, like this: `d[i][j]` -> can be reached from `d[i][k] + d[k][j]` 
> so first start via node 0 -> so 0 to 0 is 0, and 0 -> 1  would be 0 -> 0 and 0 -> 1 and so on. Now lets say 1 -> 2 via 0, 1 -> 0 + 0 -> 2 ,so via 0 means this and like this we will see if the distance can be shortened via every node.
> ![[Pasted image 20250628184440.png|400]]
> The final matrix gives shortest path from any node to any node
> How does it detect negative cycles -> the nodes should have distance to itself 0 but after iteration of via, it will become negative
> 

```cpp
class Solution {
  public:
    void floydWarshall(vector<vector<int>> &dist) {
        //first the adj matrix should have 1e8 for edges not connected
        //which is done here already
        int n = dist.size();
        for(int via = 0; via<n; via++){
            for(int i = 0;i<n;i++){
                for(int j = 0;j<n;j++){
                    if(dist[i][via] != 1e8 && dist[via][j] != 1e8)
                        dist[i][j] = min(dist[i][j], dist[i][via] + dist[via][j]);
                }
            }
        }
        
        //detecting cycles
        for(int i = 0;i<n;i++){
            if(dist[i][i] < 0) {
                //-ve cycle (although no -ve cycle mentioned in q here)
            }
        }
        
    }
};
```
T.C = O($N^3$) 
S.C = O($N^2$) (editing the given matrix also counts as using space)

Still better approach is to apply Djikstra on each node that is better time complexity, but doesn't work on negative edges

---
##### Find the City With the Smallest Number of Neighbors at a Threshold Distance
link - [Find the City With the Smallest Number of Neighbors at a Threshold Distance](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/)

```cpp
class Solution {
public:
    int findTheCity(int n, vector<vector<int>>& edges, int distanceThreshold) {
        // applying Floyd-Warshall, can apply Dijkstra on each node as well
        vector<vector<int>> matrix(n, vector<int>(n, INT_MAX));

        for (int i = 0; i < n; i++) {
            matrix[i][i] = 0;
        }

        for (auto it : edges) {
            int u = it[0];
            int v = it[1];
            int wt = it[2];
            matrix[u][v] = wt;
            matrix[v][u] = wt;
        }

        for (int via = 0; via < n; via++) {
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (matrix[i][via] != INT_MAX && matrix[via][j] != INT_MAX)
                        matrix[i][j] = min(matrix[i][j], matrix[i][via] + matrix[via][j]);
                }
            }
        }

        int mini = n;
        int city = -1;
        for (int i = 0; i < n; i++) {
            int cnt = 0;
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] <= distanceThreshold) cnt++;
            }
            if (cnt <= mini) {
                mini = cnt;
                city = i;
            }
        }
        return city;
    }
};

```
