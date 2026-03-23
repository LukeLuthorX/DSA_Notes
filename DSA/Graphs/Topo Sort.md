[[Graphs]]

>Topological Sort exists only DAG - Directed Acyclic Graphs.

>→ Linear ordering of vertices such that if there is an edge between u and v, u appears before v in that ordering

##### Topo Sort Code
```cpp
class Solution {
public:
    void DFS(int node, vector<int>& visited, vector<vector<int>>& adj, stack<int>& st) {
        visited[node] = 1;
        for(auto it: adj[node]) {
            if(!visited[it]) {
                DFS(it, visited, adj, st);
            }
        }
        st.push(node);
    }
    
    vector<int> topoSort(int V, vector<vector<int>>& edges) {
        vector<int> visited(V, 0);
        vector<vector<int>> adj(V);
        for(int i = 0; i < edges.size(); i++) {
            adj[edges[i][0]].push_back(edges[i][1]);
        }
        stack<int> st;
        for(int i = 0; i < V; i++) {
            if(!visited[i]) {
                DFS(i, visited, adj, st);
            }
        }
        
        vector<int> ans;
        while(!st.empty()) {
            ans.push_back(st.top());
            st.pop();
        }
        return ans;
    }
};
```

---
##### Kahn's Algorithm (BFS)
indegree array -> maintain the incoming edges .
the nodes with 0 incoming edges can come first, so insert them in queue, then these nodes are removed from outgoing edges since the other nodes connected to these are not dependent anymore
```cpp
class Solution {
  public:
    vector<int> topoSort(int V, vector<vector<int>>& edges) {
        vector<vector<int>>adj(V);
        for(int i = 0;i<edges.size();i++){
            adj[edges[i][0]].push_back(edges[i][1]);
        }
        
        //for each node, store its no of incoming node
        vector<int>indegree(V,0);
        for(int i = 0;i<V;i++){
            for(auto it:adj[i]){
                indegree[it]++; //nuance to ponder
            }
        }
        //insert into queue the nodes with 0 incoming 
        queue<int>q;
        for(int i = 0;i<V;i++){
            if(indegree[i] == 0)q.push(i);
        }
        
        vector<int>topo;
        while(!q.empty()){
            int node = q.front();
            q.pop();
            topo.push_back(node);
            for(auto it: adj[node]){
                indegree[it]--;
                if(indegree[it] == 0)q.push(it);
            }
                
        }
        return topo;
        
    }
};
```

---
##### Cycle Detection in Directed Graph (BFS)
for this we use Kahn's algo, if there is a cycle, the queue wont ever return N vertices, if it has N size, then its DAG (Directed Acyclic Graph) -> No cycle
```cpp
class Solution {
  public:
    bool isCyclic(int V, vector<vector<int>> &edges) {
        vector<vector<int>>adj(V);
        for(int i = 0;i<edges.size();i++){
            adj[edges[i][0]].push_back(edges[i][1]);
        }
        
        vector<int>indegree(V,0);
        for(int i = 0;i<V;i++){
            for(auto it:adj[i]){
                indegree[it]++;
            }
        }
        
        queue<int>q;
        for(int i = 0;i<V;i++){
            if(indegree[i] == 0)q.push(i);
        }
        
        int cnt = 0;
        
        while(!q.empty()){
            int node = q.front();
            q.pop();
            cnt++;
            for(auto it: adj[node]){
                indegree[it]--;
                if(indegree[it] == 0)q.push(it);
            }
        }
        if(cnt == V) return false;
        return true;
        
    }
};
```

---
##### Course Schedule 
link - [Course Schedule](https://leetcode.com/problems/course-schedule/)
if there is a cycle then deadlock.
```cpp
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        // making adj list from edges
        vector<vector<int>> adj(numCourses);
        for (int i = 0; i < prerequisites.size(); i++) {
            adj[prerequisites[i][1]].push_back(prerequisites[i][0]);
        }

        // keep track of incoming edges for all nodes
        vector<int> indegree(numCourses);
        for (int i = 0; i < numCourses; i++) {
            for (auto it : adj[i]) {
                indegree[it]++;
            }
        }

        queue<int> q;
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) q.push(i);
        }

        int cnt = 0;
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            cnt++;
            for (auto it : adj[node]) {
                indegree[it]--; // remove outgoing for nodes connected to it
                if (indegree[it] == 0) q.push(it);
            }
        }

        return cnt == numCourses;
    }
};

```

---
##### Course Scheduling II
link - [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)

```cpp
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> adj(numCourses);
        for (int i = 0; i < prerequisites.size(); i++) {
            adj[prerequisites[i][1]].push_back(prerequisites[i][0]);
        }

        vector<int> indegree(numCourses);
        for (int i = 0; i < numCourses; i++) {
            for (auto it : adj[i]) {
                indegree[it]++;
            }
        }

        queue<int> q;
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) q.push(i);
        }

        vector<int> topo;
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            topo.push_back(node);
            for (auto it : adj[node]) {
                indegree[it]--;
                if (indegree[it] == 0) q.push(it);
            }
        }

        if (topo.size() != numCourses) return {};
        return topo;
    }
};
```

---
##### Find Eventual Safe States
link - [Find Eventual Safe States](https://leetcode.com/problems/find-eventual-safe-states/)
```cpp
class Solution {
public:
    vector<int> eventualSafeNodes(vector<vector<int>>& graph) {
        // reverse all the edges of graph, and put terminal in queue
        // then perform topo sort
        int n = graph.size();
        vector<int> indegree(n);
        vector<vector<int>> adjRev(n);
        for (int i = 0; i < n; i++) {
            // i -> it
            for (auto it : graph[i]) {
                // it -> i (reverse edge)
                adjRev[it].push_back(i);
                indegree[i]++;
            }
        }

        // now terminal nodes are ones with no incoming edge
        queue<int> q;
        for (int i = 0; i < n; i++) {
            if (indegree[i] == 0) q.push(i);
        }

        vector<int> safe;
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            safe.push_back(node);
            for (auto it : adjRev[node]) {
                indegree[it]--;
                if (indegree[it] == 0) q.push(it);
            }
        }

        sort(safe.begin(), safe.end());
        return safe;
    }
};
```

---
##### Alien Dictionary
link - [Alien Dictionary](https://www.geeksforgeeks.org/problems/alien-dictionary/1)
lexicographically comparison is done between words on the first letter they differ, in english a comes before b, so aba is smaller than bba, but in the alien language, the lexicographical comparison is different and that's what we have to find from the words given. so we compare pair of words and treat the letters as nodes and the order is compared from the words and this becomes a topo sort problem.

```cpp
class Solution {
  public:
    string findOrder(vector<string> &words) {
        vector<vector<int>> adj(26);             
        vector<int> indegree(26, 0);             
        vector<int> present(26, 0);         
        
        // Mark present characters
        for (string &word : words) {
            for (char ch : word) {
                present[ch - 'a'] = 1;
            }
        }

        // Build adjacency list based on first differing characters
        int n = words.size();
        for (int i = 0; i < n - 1; i++) {
            string s1 = words[i];
            string s2 = words[i + 1];
            int len = min(s1.size(), s2.size());
            bool flag = false;

            for (int ptr = 0; ptr < len; ptr++) {
                if (s1[ptr] != s2[ptr]) {
                    adj[s1[ptr] - 'a'].push_back(s2[ptr] - 'a');
                    indegree[s2[ptr] - 'a']++;
                    flag = true;
                    break;
                }
            }

            //word1 is longer and is a prefix of word2
            if (!flag && s1.size() > s2.size()) return "";
        }

        //topo sort
        queue<int> q;
        for (int i = 0; i < 26; i++) {
            if (present[i] && indegree[i] == 0) {
                q.push(i);
            }
        }

        string ans = "";
        while (!q.empty()) {
            int node = q.front(); q.pop();
            ans += char(node + 'a');
            for (int it : adj[node]) {
                indegree[it]--;
                if (indegree[it] == 0) {
                    q.push(it);
                }
            }
        }

        int total_chars = 0;
        for (int i = 0; i < 26; i++) {
            if (present[i]) total_chars++;
        }

        if (ans.size() != total_chars) {
            return ""; // cycle exists
        }

        return ans;
    }
};
```



