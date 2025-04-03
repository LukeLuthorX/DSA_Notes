[[Graphs]]

> [!NOTE]+ Some Basic Stuff
> - undirected/directed graph
> - cyclic/acyclic graph
> - **Path** - the same node cannot be visited twice 
> - **degree of a node** - number of edges attached to it
> - **Total degree of a graph** = 2 $\times$ E
> - *In directed graph* - **Indegree** (no. of edges coming to a node) and **Outdegree** (no. of edges going out of a node)

> [!NOTE]+ graph representation
>  - **Adjacency Matrix** - `adj[n+1][m+1]` - O(N$^2$) space
> ```cpp
> int n, m;
> cin >> n >> m;
> for(int i = 0;i<m;i++){
> 	int u, v;
> 	cin >> u >> v;
> 	adj[u][v] = 1;
> 	adj[v][u] = 1;
> }
> ```
> 
> - **Adjacency List** - vector<int>adj[n+1] (array of vectors), for each node, its neighbours are stored in the vector. Space - *O(2E)*
> ```cpp
> int n, m;
> cin>>n>>m;
> vector<int> adj[n+1]
> for(int i = 0;i<m; i++){
> 	int u, v;
> 	cin>>u>>v;
> 	adj[u].push_back(v);
> 	adj[u].push_back(v)
> }
> ```
---

##### BFS
```cpp
vector<int> bfsOfGraph(vector<vector<int>> &adj) {
	queue<int> q;
	q.push(0);
	vector<int>visited(adj.size(),0);
	visited[0] = 1;
	vector<int>bfs;
	while(!q.empty()){
		int node = q.front();
		q.pop();
		bfs.push_back(node);
		for(auto it: adj[node]){
			if(!visited[it]){
				visited[it] = 1;
				q.push(it);
			}
		}
	}
	return bfs;
}
```
SC = O(N)
TC =  O(V + 2E) (loop running for all neighbours -> edges)

---
##### DFS
```cpp
class Solution {
  public:
    void dfs(int node,vector<int>&visited, vector<int>&ls,vector<vector<int>>& adj){
        visited[node] = 1;
        ls.push_back(node);
        for(auto it: adj[node]){
            if(!visited[it]){
                dfs(it,visited,ls,adj);
            }
        }
    }
    vector<int> dfsOfGraph(vector<vector<int>>& adj) {
        vector<int>visited(adj.size(),0);
        vector<int>ls;
        dfs(0,visited,ls,adj);
        return ls;
    }
};

```
SC = O(N)
TC = O(V + 2E) 

