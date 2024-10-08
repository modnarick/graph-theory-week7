# Problem 1 - Travelling Salesman Problem
  
``` cpp

#include <bits/stdc++.h>
using namespace std;

const int INF = INT_MAX;

struct Edge {
    int id;
    int u, v;
    int cost;
};

void tsp(int n, vector<vector<int>>& graph, int start, vector<Edge>& edges, unordered_map<int, int>& edge_map) {
    vector<int> vertices;
    for (int i = 0; i < n; i++) {
        if (i != start) {
            vertices.push_back(i);
        }
    }

    int min_cost = INF;
    vector<int> best_path_edges;

    do {
        int current_cost = 0;
        int current_vertex = start;
        vector<int> current_path_edges;

        for (int i = 0; i < vertices.size(); i++) {
            current_cost += graph[current_vertex][vertices[i]];
            current_path_edges.push_back(edge_map[current_vertex * n + vertices[i]]);
            current_vertex = vertices[i];
        }

        current_cost += graph[current_vertex][start];
        current_path_edges.push_back(edge_map[current_vertex * n + start]);

        if (current_cost < min_cost) {
            min_cost = current_cost;
            best_path_edges = current_path_edges;
        }

    } while (next_permutation(vertices.begin(), vertices.end()));

    cout << "Cost: " << min_cost << endl;
    cout << "Route: ";
    for (int i = 0; i < best_path_edges.size(); i++) {
        cout << best_path_edges[i];
        if (i != best_path_edges.size() - 1) {
            cout << ", ";
        }
    }
    cout << endl;
}

int main() {
    int n, e;
    
    cin >> n >> e;

    vector<vector<int>> graph(n, vector<int>(n, INF));
    vector<Edge> edges(e);
    unordered_map<int, int> edge_map;

    for (int i = 0; i < e; i++) {
        int edge, u, v, cost;
        cin >> edge >> u >> v >> cost;
        edges[i] = {edge, u - 1, v - 1, cost};
        graph[u - 1][v - 1] = cost;
        graph[v - 1][u - 1] = cost;
        edge_map[(u - 1) * n + (v - 1)] = edge;
        edge_map[(v - 1) * n + (u - 1)] = edge;
    }
    
    int start_node;
    cin >> start_node;
    start_node -= 1;

    tsp(n, graph, start_node, edges, edge_map);

    return 0;
}
}
```
  
# Problem 2 - Chinese Postman Problem
  
``` cpp
#include <bits/stdc++.h>
using namespace std;

const int INF = INT_MAX;

struct Edge {
    int id;
    int u, v;
    int cost;
};

void findRoute(int startNode, const vector<Edge>& edges, unordered_map<int, vector<pair<int, int>>>& adj, vector<int>& route) {
    stack<int> stack;
    stack.push(startNode);
    vector<int> currentPath;
    unordered_map<int, int> visitedEdges;

    while (!stack.empty()) {
        int current = stack.top();
        bool hasEdge = false;

        while (!adj[current].empty()) {
            auto next = adj[current].back();
            int edgeId = next.first;
            int nextVertex = next.second;

            if (visitedEdges[edgeId] < 1) {
                visitedEdges[edgeId]++;
                stack.push(nextVertex);
                currentPath.push_back(edgeId);
                adj[current].pop_back();
                current = nextVertex;
                hasEdge = true;
                break;
            } else {
                adj[current].pop_back();
            }
        }

        if (!hasEdge) {
            stack.pop();
        }
    }

    route = currentPath;
}

int main() {
    int n, e;

    cin >> n >> e;

    vector<Edge> edges(e);
    vector<int> degree(n, 0);
    unordered_map<int, vector<pair<int, int>>> adj;

    for (int i = 0; i < e; i++) {
        int edge, u, v, cost;
        cin >> edge >> u >> v >> cost;
        edges[i] = {edge, u - 1, v - 1, cost};
        degree[u - 1]++;
        degree[v - 1]++;
        
        adj[u - 1].emplace_back(edge, v - 1);
        adj[v - 1].emplace_back(edge, u - 1);
    }

    int totalCost = 0;
    for (const auto& edge : edges) {
        totalCost += edge.cost;
    }

    vector<int> oddVertices;
    for (int i = 0; i < n; i++) {
        if (degree[i] % 2 == 1) {
            oddVertices.push_back(i);
        }
    }

    int additionalCost = 0;
    if (oddVertices.size() == 2) {
        int u = oddVertices[0], v = oddVertices[1];
        vector<vector<int>> dist(n, vector<int>(n, INT_MAX));

        for (const auto& edge : edges) {
            dist[edge.u][edge.v] = edge.cost;
            dist[edge.v][edge.u] = edge.cost;
        }

        for (int k = 0; k < n; k++) {
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (dist[i][k] < INT_MAX && dist[k][j] < INT_MAX) {
                        dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
                    }
                }
            }
        }

        additionalCost = dist[u][v];
    }

    int totalFinalCost = totalCost + additionalCost;

    vector<int> route;
    
    unordered_map<int, vector<pair<int, int>>> orderedAdj;
    for (const auto& edge : edges) {
        orderedAdj[edge.u].emplace_back(edge.id, edge.v);
        orderedAdj[edge.v].emplace_back(edge.id, edge.u);
    }

    findRoute(0, edges, orderedAdj, route);

    if (!route.empty()) {
        route.push_back(route[0]);
    }

    cout << "Cost: " << totalFinalCost << endl;
    cout << "Route: ";
    for (int i = 0; i < route.size(); i++) {
        cout << edges[route[i]].id;
        if (i != route.size() - 1) {
            cout << ", ";
        }
    }
    cout << endl;

    return 0;
}


```
  
# Problem 3 - The Knight's Tour
  
``` cpp
#include <bits/stdc++.h>
using namespace std;

int dx[8] = {2, 1, -1, -2, -2, -1, 1, 2};
int dy[8] = {1, 2, 2, 1, -1, -2, -2, -1};

bool validMove(int x, int y, int N, vector<vector<int>>& visited) {
    return (x >= 0 && x < N && y >= 0 && y < N && visited[x][y] == -1);
}

int remainingMoves(int x, int y, int N, vector<vector<int>>& visited) {
    int count = 0;
    for (int i = 0; i < 8; ++i) {
        int nextX = x + dx[i];
        int nextY = y + dy[i];
        if (validMove(nextX, nextY, N, visited)) {
            count++;
        }
    }
    return count;
}

void printPath(vector<pair<int, int>>& path) {
    for (const auto& move : path) {
        cout << move.first << " " << move.second << endl;
    }
}

bool knightsTour(int x, int y, int moveCount, int N, vector<vector<int>>& visited, vector<pair<int, int>>& path) {
    if (moveCount == N * N) {
        return true;
    }

    vector<pair<int, pair<int, int>>> moves;
    for (int i = 0; i < 8; ++i) {
        int nextX = x + dx[i];
        int nextY = y + dy[i];
        if (validMove(nextX, nextY, N, visited)) {
            int count = remainingMoves(nextX, nextY, N, visited);
            moves.push_back({count, {nextX, nextY}});
        }
    }

    sort(moves.begin(), moves.end());

    for (auto& move : moves) {
        int nextX = move.second.first;
        int nextY = move.second.second;
        visited[nextX][nextY] = moveCount;
        path.push_back({nextX, nextY});

        if (knightsTour(nextX, nextY, moveCount + 1, N, visited, path)) {
            return true;
        }

        visited[nextX][nextY] = -1;
        path.pop_back();
    }

    return false;
}

int main() {
    int N;
    cin >> N;

    int startX, startY;
    cin >> startX >> startY;

    if (startX < 0 || startX >= N || startY < 0 || startY >= N)
        cout << "No solution found!" << endl;
    else {
        vector<vector<int>> visited(N, vector<int>(N, -1));
        visited[startX][startY] = 0;
        vector<pair<int, int>> path;
        path.push_back({startX, startY});

        if (knightsTour(startX, startY, 1, N, visited, path)) {
            printPath(path);
        }
        else {
        cout << "No solution found!" << endl;
        }
    }
    return 0;
}
```




