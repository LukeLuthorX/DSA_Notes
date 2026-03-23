[[Graphs]]

##### Tarjan's Algorithm (Bridges in a Graph)
link - [Critical Connections in a Network](https://leetcode.com/problems/critical-connections-in-a-network/)
bridge -> if graph is broken down into 2 or more components upon removal of an edge
- step at which you reach a node (DFS insertion time)-> `tin[node]`
- minimum of insertion time of all adjacent nodes apart from parent -> `low[node]`
- you go on performing dfs and marking the time and low for the step it is in and other comparisons and calculations happen in backtracking where the node compares its low value with the adjacent and upon going back to the parent, if the child's low value is greater than the parent's then the child node can't visit nodes which were visited earlier (with lesser value of low) thus can't reach without the edge with parent -> bridge
![[Pasted image 20250630235623.png | 400]]

```cpp
class Solution {
private:
    int timer = 1;

private:
    void DFS(int node, int parent, vector<int>& vis, vector<vector<int>>& adj, vector<int>& tin, vector<int>& low, vector<vector<int>>& bridges) {
        vis[node] = 1;
        tin[node] = low[node] = timer;
        timer++;

        for (auto it : adj[node]) {
            if (it == parent) continue; // Skip checking the parent in backtracking

            if (vis[it] == 0) {
                DFS(it, node, vis, adj, tin, low, bridges);
                // Code from this line executes during backtracking
                low[node] = min(low[node], low[it]);
                // Check if (node --- it) is a bridge
                if (low[it] > tin[node]) {
                    // If 'it' cannot reach 'node' before its insertion time using the lowest insertion time of connected nodes, then this is a bridge
                    bridges.push_back({node, it});
                }
            } else {
                // If 'it' is already visited, just update low
                low[node] = min(low[node], tin[it]);
            }
        }
    }

public:
    vector<vector<int>> criticalConnections(int n, vector<vector<int>>& connections) {
        vector<vector<int>> adj(n);
        for (auto it : connections) {
            adj[it[0]].push_back(it[1]);
            adj[it[1]].push_back(it[0]);
        }

        vector<int> vis(n, 0);
        vector<int> tin(n);
        vector<int> low(n);
        vector<vector<int>> bridges;

        DFS(0, -1, vis, adj, tin, low, bridges);
        return bridges;
    }
};
```

---
##### Articulation Point
link - [Articulation Point](https://www.geeksforgeeks.org/problems/articulation-point-1/1)

->Nodes on whose removal graph breaks into multiple components
`tin[node]` -> store DFS insertion time
`low[node]` -> min of all adjacent nodes apart from parent & visited nodes
- **Discover** nodes one by one, giving each a timestamp.
- **Track** the “lowest” timestamp reachable from each subtree.
- **If** plugging into your subtree’s only entry point (the edge from you to that child) is the only way in—i.e. no alternative back‑route—then you’re critical.
- **Root nodes** need two separate branches to be critical.

```cpp
class Solution {
  private:
    int timer = 0;
    void DFS(int node, int parent,vector<int>&vis, vector<int>adj[],vector<int>&tin, vector<int>&low, vector<int>&mark){
        vis[node] = 1;
        tin[node] = low[node] = timer;
        timer++;
        
        int child = 0;
        for(auto it:adj[node]){
            if(it == parent) continue;
            if(!vis[it]){
                DFS(it,node,vis,adj,tin,low,mark);
                low[node] = min(low[it],low[node]);
                if(low[it] >= tin[node] && parent != -1){
                    mark[node] = 1;
                }
                child++;
            }
            else{
                low[node] = min(low[node], tin[it]);
            }
        }
        if(child > 1 && parent == -1){
            mark[node] = 1;
            //this is the case when root node has for eg 2 children,so they 
            //would be distributed as well, so root is AP as well
        }
    }
  public:
    vector<int> articulationPoints(int V, vector<int> adj[]) {
        vector<int>vis(V,0);
        vector<int>tin(V);
        vector<int>low(V);
        vector<int>mark(V,0); //so that we dont put the same articulation point more than once
        
        for(int i = 0; i<V;i++){
            if(!vis[i]){
                DFS(i,-1,vis,adj,tin,low,mark);
            }
        }
        
        vector<int> ans;
        for(int i = 0;i<V;i++){
            if(mark[i] == 1) ans.push_back(i);
        }
        
        if(ans.size() == 0) return {-1}; 
        return ans;
    }
};
```

---
##### Kosaraju's Algorithm

Strongly Connected Component (SCC) - only valid in directed graphs -> means each node can be visited from every other node in SCC
![[Pasted image 20250701012901.png | 400]]
Kosaraju Algo -> Find all SCC in a graph
`scc1 -> scc2 -> scc3 -> scc4 `
reverse the entire graph (each scc still holds) and then reversal would stop one scc to reach another scc
**steps**
	- sort all the edges according to finish time (this gives order of all scc nodes)
	- reverse all the edges
	- do a dfs
```cpp
class Solution {
  private:
    void DFS(int node, vector<int>&vis, vector<vector<int>>&adj,stack<int>&st){
        vis[node] = 1;
        for(auto it: adj[node]){
            if(!vis[it]){
                DFS(it,vis,adj,st);
            }
        }
        //to sort by finish time
        st.push(node); //the node finished last goes at top(its root node)
    }
    
    void DFS3(int node, vector<int>&vis, vector<vector<int>>&adjT){
        vis[node] = 1;
        for(auto it: adjT[node]){
            if(!vis[it]){
                DFS3(it,vis,adjT);
            }
        }
    }
  public:
    int kosaraju(vector<vector<int>> &adj) {
        int n = adj.size();
        vector<int>vis(n,0);
        
        stack<int>st;
        for(int i = 0;i<n;i++){
            if(!vis[i]){
                DFS(i,vis,adj,st);
            }
        }
        
        //reversing edges
        vector<vector<int>>adjT(n);
        for(int i = 0;i<n;i++){
            vis[i] = 0; //so that I can reuse this
            for(auto it:adj[i]){
              //i --> it to reverse = it --> i
              adjT[it].push_back(i);
            }
        }
        
        int scc = 0;    
        while(!st.empty()){
            int node = st.top();
            st.pop();
            if(!vis[node]){
                scc++;
                DFS3(node,vis,adjT);
            }
        }
        return scc;
    }
};
```

