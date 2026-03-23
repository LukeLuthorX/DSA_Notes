[[Graphs]]

> spanning tree definition - A tree which has $N$ Nodes and $N-1$ edges, all nodes are reachable from each other. The sum of edges is least then its minimum spanning tree.

##### Prim's Algorithm

> [!NOTE]- Intuition
> ![[Pasted image 20250629000739.png|400]]
> 
![[Pasted image 20250629000920.png|500]]
> Intuition is **Greedy**, we know each node is included in MST, so some node connecting to it has to be taken in MST, so we put all its connected node and edges in priority queue which gives the shortest edge, so we choose it and do this for all the nodes and get MST

```cpp
class Solution {
  public:
    // Function to find sum of weights of edges of the Minimum Spanning Tree.
    int spanningTree(int V, vector<vector<int>> adj[]) {
        vector<int>vis(V,0);
        
        priority_queue<pair<int,int>, 
        vector<pair<int,int>>, 
        greater<pair<int,int>>>pq;
        //{wt, node} -> need the smallest edge connecting
        //to any other node from this node
        pq.push({0,0});
        int sum = 0;
        while(!pq.empty()){
            auto it = pq.top();
            pq.pop();
            int wt = it.first;
            int node = it.second;
            
            if(vis[node] == 1) continue;
            
            vis[node] = 1;//this node is included if its
            sum += wt;//not have been visited yet, since pq
            //gave smallest wt value edge node only
            
            //now put all its neighbours in pq to 
            //get smallest edge
            for(auto itr:adj[node]){
                int adjNode = itr[0];
                int edW = itr[1];
                if(!vis[adjNode]){
                    pq.push({edW,adjNode});
                }
            }
        }
        return sum;
    }
};
```

T.C = we perform for E edges (while loop) and pq takes logE (pq.top()) time to process and then for the neighbours, ElogE (search and push in pq)
    = $O(E \times log(E))$
---

 ##### Disjoint Set (Most Important Topic of Graph)
  

> [!NOTE]- Note
>   - If a graph is given and have to find whether node 1 and 5 are connected, so we will do BFS/DFS which will take O(V + 2E). But disjoint set tells it in constant time
>   - Disjoint Set is a data structure gives two functions - find parent and union (by rank or by size). Used in **Dynamic Connecting Graphs**
>   - Union connects nodes. and helps make graph and in between before the entire graph is formed, it can tell whether that node is connected or not in constant time
>   - Time complexity of DS is $4  \alpha$  where $\alpha$ is nearly equal to 1 so constant time
>   - ![[Pasted image 20250629010322.png | 400]]
> 	  - *find ultimate parent of u & v, pu ,pv (parent of u and v), go edge wise*
> 	  - *find rank of pu and pv (rank is the height below the node (kind of))*
> 	  - *connect smaller rank to larger rank parent node (since larger set's parent finding of nodes will take more time since its longer), while this graph is being built, different nodes belong to same component or not can be known*
>   - since finding ultimate parent for each node takes upto log(N) time, so **path compression** is done. Meaning, for each node its ultimate parent is mapped, visiting parent recursively till parent and node is same (ultimate parent.). Now finding parent takes constant time after this.
>   - Rank in DSU is an approximation of the height of a set's tree structure.

###### Disjoint Set with Rank
```cpp
#include <bits/stdc++.h>
using namespace std;

class DisjointSet {
	vector<int> rank, parent;
public:
	//constructor
	DisjointSet(int n) {
		//n+1 works for both 0 and 1 type indexing
		rank.resize(n + 1, 0);
		parent.resize(n + 1);
		for (int i = 0; i <= n; i++) {
			parent[i] = i;
		}
	}

	int findUPar(int node) {
		if (node == parent[node])
			return node; //ultimate parent
		//by assigning, path compression is done
		return parent[node] = findUPar(parent[node]);
	}

	void unionByRank(int u, int v) {
		int ulp_u = findUPar(u);
		int ulp_v = findUPar(v);
		//if parents are same for both, already in union
		if (ulp_u == ulp_v) return;

		//if rank of u is less than the rank of v (parent) then do union of smaller with larger
		if (rank[ulp_u] < rank[ulp_v]) {
			parent[ulp_u] = ulp_v;
			//rank doesn't increase when shorter tree is attached to longer
		}
		else if (rank[ulp_v] < rank[ulp_u]) {
			parent[ulp_v] = ulp_u;
		}
		else {
		//if same height(rank) then mergin will increase height by 1
			parent[ulp_v] = ulp_u;
			rank[ulp_u]++;
		}
	}
};

int main() {
#ifndef ONLINE_JUDGE
	freopen("input.txt", "r", stdin);
	freopen("output.txt", "w", stdout);
#endif
	DisjointSet ds(7);
	ds.unionByRank(1, 2);
	ds.unionByRank(2, 3);
	ds.unionByRank(4, 5);
	ds.unionByRank(6, 7);
	ds.unionByRank(5, 6);

	//if 3 and 7 same or not
	if (ds.findUPar(3) == ds.findUPar(7)) {
		cout << "same\n";
	}
	else cout << "not same\n";

	ds.unionByRank(3, 7);

	if (ds.findUPar(3) == ds.findUPar(7)) {
		cout << "same\n";
	}
	else cout << "not same\n";
	return 0;
}
```

###### Disjoint Set by Size
>more inuitive, just the number of nodes

```cpp
#include <bits/stdc++.h>
using namespace std;

class DisjointSet {
	vector<int> size, parent;
public:
	//constructor
	DisjointSet(int n) {
		//n+1 works for both 0 and 1 type indexing
		size.resize(n + 1, 0);
		parent.resize(n + 1);
		for (int i = 0; i <= n; i++) {
			parent[i] = i;
			size[i] = 1; //each node itself
		}
	}

	int findUPar(int node) {
		if (node == parent[node])
			return node; //ultimate parent
		//by assigning, path compression is done
		return parent[node] = findUPar(parent[node]);
	}

	void unionBySize(int u, int v) {
		int ulp_u = findUPar(u);
		int ulp_v = findUPar(v);
		//if parents are same for both, already in union
		if (ulp_u == ulp_v) return;

		//if rank of u is less than the rank of v (parent) then do union of smaller with larger
		if (size[ulp_u] < size[ulp_v]) {
			parent[ulp_u] = ulp_v;
			size[ulp_v] += size[ulp_u];
		}
		else if (size[ulp_v] < size[ulp_u]) {
			parent[ulp_v] = ulp_u;
			size[ulp_u] += size[ulp_v];
		}
		else {
			parent[ulp_v] = ulp_u;
			size[ulp_u] += size[ulp_v];
		}
	}
};

int main() {
#ifndef ONLINE_JUDGE
	freopen("input.txt", "r", stdin);
	freopen("output.txt", "w", stdout);
#endif
	DisjointSet ds(7);
	ds.unionBySize(1, 2);
	ds.unionBySize(2, 3);
	ds.unionBySize(4, 5);
	ds.unionBySize(6, 7);
	ds.unionBySize(5, 6);

	//if 3 and 7 same or not
	if (ds.findUPar(3) == ds.findUPar(7)) {
		cout << "same\n";
	}
	else cout << "not same\n";

	ds.unionBySize(3, 7);

	if (ds.findUPar(3) == ds.findUPar(7)) {
		cout << "same\n";
	}
	else cout << "not same\n";
	return 0;
}
```

---

##### Kruskal's Algorithm
- sort all edges according to weight (wt, u, v)
- Now, using disjoint set, for all edges (sorted) we check if they are from same set or not, if not then union (initally all nodes have parent as self)
```cpp
class DisjointSet{
  vector<int>size,parent;
  public:
    DisjointSet(int n){
        parent.resize(n+1);
        size.resize(n+1);
        for(int i = 0;i<=n;i++){
            parent[i] = i; 
            size[i] = 1;
        }
    }
    
    int findUParent(int node){
        if(parent[node] == node) return node;
        return parent[node] = findUParent(parent[node]);
    }
    
    void unionBySize(int u, int v){
        int uparent_u = findUParent(u);
        int uparent_v = findUParent(v);
        if(uparent_u == uparent_v) return;
        
        if(size[uparent_u] < size[uparent_v]){
            parent[uparent_u] = uparent_v;
            size[uparent_v] += size[uparent_u];
        }
        else if(size[uparent_u] > size[uparent_v]){
            parent[uparent_v] = uparent_u;
            size[uparent_u] += size[uparent_v];
        }
        else{
            parent[uparent_v] = uparent_u;
            size[uparent_u] += size[uparent_v];
        }
    }
};

class Solution {
  public:
    
    int spanningTree(int V, vector<vector<int>> adj[]) {
        //making edges vector
        vector<pair<int,pair<int,int>>>edges;
        for(int i = 0;i<V;i++){
            for(auto it:adj[i]){
                int u = i;
                int v = it[0];
                int wt = it[1];
                edges.push_back({wt,{u,v}});
            }
        }
        
        DisjointSet ds(V);
        //sort the edges by weight
        sort(edges.begin(),edges.end());
        int minwt = 0;
        
        for(auto it: edges){
            int wt = it.first;
            int u = it.second.first;
            int v = it.second.second;
            //if the nodes are not in the same set, then take it 
            //into MST and then union it and do the same for other edges
            if(ds.findUParent(u) != ds.findUParent(v)){
                minwt += wt;
                ds.unionBySize(u,v);
            }
        }
        return minwt;
        
    }
};
```

---
##### Number of Operations to Make Network Connected
link - [Number of Operations to Make Network Connected](https://leetcode.com/problems/number-of-operations-to-make-network-connected/)

```cpp
class DisjointSet {
public:
    vector<int> parent, size;

    DisjointSet(int n) {
        parent.resize(n + 1);
        size.resize(n + 1);
        for (int i = 0; i <= n; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    int findUParent(int node) {
        if (parent[node] == node) return node;
        return parent[node] = findUParent(parent[node]);
    }

    void unionBySize(int u, int v) {
        int parent_u = findUParent(u);
        int parent_v = findUParent(v);
        if (parent_u == parent_v) return;

        if (size[parent_u] < size[parent_v]) {
            parent[parent_u] = parent_v;
            size[parent_v] += size[parent_u];
        } else if (size[parent_u] > size[parent_v]) {
            parent[parent_v] = parent_u;
            size[parent_u] += size[parent_v];
        } else {
            parent[parent_v] = parent_u;
            size[parent_u] += size[parent_v];
        }
    }
};

class Solution {
public:
    int makeConnected(int n, vector<vector<int>>& connections) {
        // if there are components not connected, then the minimum number of edges
        // required is number of components - 1, so we just need to know
        // whether extra edges are present -> use Disjoint set

        DisjointSet ds(n);
        int extra = 0;

        for (auto it : connections) {
            int u = it[0];
            int v = it[1];
            if (ds.findUParent(u) == ds.findUParent(v)) {
                // have same parent in dynamically generated graph,
                // already connected then this is an extra edge can be used to connect unconnected component
                extra++;
            } else {
                // connect them dynamically
                ds.unionBySize(u, v);
            }
        }

        // to find components in different sets after all DS operations
        // to find the number of unconnected components, the parent[i] will point to itself
        int unconnected = 0;
        for (int i = 0; i < n; i++) {
            if (ds.parent[i] == i) unconnected++;
        }

        if (unconnected - 1 <= extra) {
            return unconnected - 1;
        }
        return -1;
    }
};
```

---
##### Most Stones Removed with Same Row or Column
link - [Most Stones Removed with Same Row or Column](https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/)
![[Pasted image 20250629031006.png | 400]]
if can connect nodes, then for that component, all nodes (stones) except one can be removed.
if one component c1 has n1 node and c2 has n2 node then stones that can be removed would be (n1 - 1) + (n2 - 1) + - - -. now merge this : (n1 + n2 + n3 ....) - (1 + 1 + 1 ....) = **no. of nodes - no. of components** 

treat the entire row as one node, and then for a 4 x 4 matrix, there would be 4 nodes that are row and then 4 columns that are nodes.
0,1,2,3 -> rows are now nodes and if stones belong to row 0 then it belongs to node 0
4,5,6,7 -> columns

eg- node 0 and node 5 (row 0 and col 0) are checked to have stones (this would make connected component leaving only one stone and others removed) 
we see where the stones lie then we connect their nodes in disjoint set
![[Pasted image 20250629234247.png|400]]
```cpp
class DisjointSet {
public:
    vector<int> parent, size;

    DisjointSet(int n) {
        parent.resize(n + 1);
        size.resize(n + 1);
        for (int i = 0; i <= n; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    int findUParent(int node) {
        if (parent[node] == node) return node;
        return parent[node] = findUParent(parent[node]);
    }

    void unionBySize(int u, int v) {
        int parent_u = findUParent(u);
        int parent_v = findUParent(v);

        if (parent_u == parent_v) return;

        if (size[parent_u] < size[parent_v]) {
            parent[parent_u] = parent_v;
            size[parent_v] += size[parent_u];
        } else {
            parent[parent_v] = parent_u;
            size[parent_u] += size[parent_v];
        }
    }
};

class Solution {
public:
    int removeStones(vector<vector<int>>& stones) {
        // Coordinates for matrix are not given
        // So finding the last stone for reference
        int maxRow = 0;
        int maxCol = 0;
        for (auto it : stones) {
            maxRow = max(maxRow, it[0]);
            maxCol = max(maxCol, it[1]);
        }

        DisjointSet ds(maxRow + maxCol + 1);
        unordered_map<int, int> stoneNodes;
        // Map to maintain the nodes which are involved with stones only
        for (auto it : stones) {
            // Wherever there is a stone, we perform union of the nodes it's connected to
            // Nodes here are just rows and cols
            // This way all the stones in connected components come together under ultimate parents
            int nodeRow = it[0];
            int nodeCol = it[1] + maxRow + 1; // If rows are 0,1,2 then col should be node 3 onwards
            ds.unionBySize(nodeRow, nodeCol);
            stoneNodes[nodeRow] = 1;
            stoneNodes[nodeCol] = 1;
        }

        int cnt = 0;
        for (auto it : stoneNodes) {
            if (ds.findUParent(it.first) == it.first) {
                cnt++;
            }
        }
        // Number of stones - number of connected components
        return stones.size() - cnt;
    }
};
```

---
##### Accounts Merge
link - [Accounts Merge](https://leetcode.com/problems/accounts-merge/)
![[Pasted image 20250630004138.png | 400]]
```cpp
class DisjointSet {
public:
    vector<int> parent, size;

    DisjointSet(int n) {
        parent.resize(n);
        size.resize(n);
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    int findUParent(int node) {
        if (parent[node] == node) return node;
        return parent[node] = findUParent(parent[node]);
    }

    void unionBySize(int u, int v) {
        int parent_u = findUParent(u);
        int parent_v = findUParent(v);

        if (parent_u == parent_v) return;

        if (size[parent_u] < size[parent_v]) {
            parent[parent_u] = parent_v;
            size[parent_v] += size[parent_u];
        } else {
            parent[parent_v] = parent_u;
            size[parent_u] += size[parent_v];
        }
    }
};

class Solution {
public:
    vector<vector<string>> accountsMerge(vector<vector<string>>& accounts) {
        int n = accounts.size();
        DisjointSet ds(n);
        // Each [name, mails...] is taken as a node
        unordered_map<string, int> mapMailNode;

        for (int i = 0; i < n; i++) {
            for (int j = 1; j < accounts[i].size(); j++) {
                // Traversing through mails
                string mail = accounts[i][j];
                if (mapMailNode.find(mail) == mapMailNode.end()) {
                    mapMailNode[mail] = i;
                    // If this mail was not on the map, then put it in and assign key as its node number
                } else {
                    // This mail has already been assigned, meaning duplicate account, perform union of accounts
                    ds.unionBySize(i, mapMailNode[mail]);
                }
            }
        }
        // Main job is done, we now know if the same duplicate accounts exist
        // Now merging the mails and discarding the duplicates

        vector<vector<string>> mergedMail(n);
        for (auto it : mapMailNode) {
            string mail = it.first;
            int node = ds.findUParent(it.second);
            mergedMail[node].push_back(mail);
        }

        vector<vector<string>> ans;
        for (int i = 0; i < n; i++) {
            if (mergedMail[i].size() == 0) continue; // Skip duplicates, we have merged them to their counterpart
            sort(mergedMail[i].begin(), mergedMail[i].end());
            vector<string> temp;
            temp.push_back(accounts[i][0]); // Putting name first
            for (auto mail : mergedMail[i]) {
                temp.push_back(mail);
            }
            ans.push_back(temp);
        }
        return ans;
    }
};
```

---
##### Number of Islands II
link - [Number of Islands II](https://www.geeksforgeeks.org/problems/number-of-islands/1)
dynamic graph - boils down to disjoint set problem
![[Pasted image 20250630013007.png | 400]]
each cell of the graph is treated as node and given number like for this 0 to 19
but how to figure out if the position given in (,) in node number - we know for this example 3rd row would be over` 2 * 5(no of cells in each row)` as first 2 rows consumed 10 nodes so 3rd row starts from next and for columns, just add -> +3 etc
`formula (row, col) = (row * m) + col = node_id`

```cpp
class DisjointSet{
public:
    vector<int>parent,size;
    DisjointSet(int n){
        parent.resize(n);
        size.resize(n);
        for(int i = 0; i<n; i++){
            parent[i] = i;
            size[i] = 1;
        }
    }
    
    int findUParent(int node){
        if(parent[node] == node) return node;
        return parent[node] = findUParent(parent[node]);
    }
    
    void unionBySize(int u, int v){
        int parent_u = findUParent(u);
        int parent_v = findUParent(v);
        
        if(parent_u == parent_v) return;
        
        if(size[parent_u] < size[parent_v]){
            parent[parent_u] = parent_v;
            size[parent_v] += size[parent_u];
        }
        else{
            parent[parent_v] = parent_u;
            size[parent_u] += size[parent_v];
        }
    }

};

class Solution {
  public:
    vector<int> numOfIslands(int n, int m, vector<vector<int>> &operators) {
        DisjointSet ds(n*m); //all cells treated as nodes
        vector<vector<int>>vis(n,vector<int>(m,0));
        int cnt = 0;
        vector<int> ans;
        
        for(auto it : operators){
            int row = it[0];
            int col = it[1];
            
            if(vis[row][col] == 1){
                //if this node has come again, no need to compute
                //it will have the same value, just push in ans
                ans.push_back(cnt);
                continue;
            }
            
            vis[row][col] = 1;
            cnt++; //no. of islands increase initially when this node is added
            //now need to check if this is part of larger island or not
            
            int delr[] = {1,0,-1,0};
            int delc[] = {0,1,0,-1};
            for(int ind = 0; ind < 4; ind++){
                int adjR = row + delr[ind];
                int adjC = col + delc[ind];
                if(adjR >= 0 && adjR < n && adjC >= 0 && adjC < m){
                    if(vis[adjR][adjC] == 1){
                        //means the node is part of larger island
                        int nodeNo = row * m + col; //its node id corresponding to disjoint set
                        int adjNodeNo = adjR * m + adjC; //adjacent node's node id
                        if(ds.findUParent(nodeNo) != ds.findUParent(adjNodeNo)){
                            //if they dont have the same ultimate parent but adjacent, means
                            //the node is now connected to its adjacent node's island group
                            //and doesnt contribute a single separate island 
                            
                            //skip if they already share a root (already merged), otherwise merge & cnt--
                            //same parent tabhi hain jab union pahele hi ho gaya hai, then nothing to do
                            //
                            cnt--;
                            ds.unionBySize(nodeNo, adjNodeNo);
                        }
                    }
                }
            }
            ans.push_back(cnt);
        }
        return ans;
    }
};
```

---
##### Making A Large Island
link - [Making A Large Island](https://leetcode.com/problems/making-a-large-island/)

![[Pasted image 20250630024555.png  | 400]]
```cpp
class DisjointSet {
public:
    vector<int> parent, size;

    DisjointSet(int n) {
        parent.resize(n);
        size.resize(n);
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    int findUParent(int node) {
        if (parent[node] == node) return node;
        return parent[node] = findUParent(parent[node]);
    }

    void unionBySize(int u, int v) {
        int parent_u = findUParent(u);
        int parent_v = findUParent(v);

        if (parent_u == parent_v) return;

        if (size[parent_u] < size[parent_v]) {
            parent[parent_u] = parent_v;
            size[parent_v] += size[parent_u];
        } else {
            parent[parent_v] = parent_u;
            size[parent_u] += size[parent_v];
        }
    }
};

class Solution {
private:
    bool isValid(int adjR, int adjC, int n) {
        return adjR >= 0 && adjR < n && adjC >= 0 && adjC < n;
    }

public:
    int largestIsland(vector<vector<int>>& grid) {
        // Step 1: Make connected components using disjoint set
        int n = grid.size();
        DisjointSet ds(n * n);

        for (int row = 0; row < n; row++) {
            for (int col = 0; col < n; col++) {
                if (grid[row][col] == 0) continue;

                int delr[] = {-1, 0, 1, 0};
                int delc[] = {0, 1, 0, -1};

                for (int ind = 0; ind < 4; ind++) {
                    int adjR = row + delr[ind];
                    int adjC = col + delc[ind];

                    if (isValid(adjR, adjC, n) && grid[adjR][adjC] == 1) {
                        int nodeNo = (row * n) + col;
                        int adjNodeNo = (adjR * n) + adjC;
                        ds.unionBySize(nodeNo, adjNodeNo);
                    }
                }
            }
        }

        // The already existing islands have been clubbed under respective parents
        // Now will try to convert 0 to 1 and see the size
        int mx = 0;
        for (int row = 0; row < n; row++) {
            for (int col = 0; col < n; col++) {
                if (grid[row][col] == 1) continue;

                set<int> components; // Using set because it can happen that a 0 is surrounded by 1's of the same island group thus repeating the number -> edge case

                int delr[] = {-1, 0, 1, 0};
                int delc[] = {0, 1, 0, -1};

                for (int ind = 0; ind < 4; ind++) {
                    int adjR = row + delr[ind];
                    int adjC = col + delc[ind];

                    if (isValid(adjR, adjC, n) && grid[adjR][adjC] == 1) {
                        // This zero is connected to an island group
                        components.insert(ds.findUParent(adjR * n + adjC));
                    }
                }

                // Once all 4 directions for that node have been traversed
                // We can check the size of the new island group
                int sizeTotal = 0; // Adding this 0 node as well so size = 1
                for (auto it : components) {
                    sizeTotal += ds.size[it];
                }
                mx = max(mx, sizeTotal + 1);
            }
        }

        // What if all the nodes are 1 then this would fail -> edge case
        // So traverse and find ultimate parent
        for (int cellNo = 0; cellNo < n * n; cellNo++) {
            mx = max(mx, ds.size[ds.findUParent(cellNo)]);
        }
        return mx;
    }
};
```

---
