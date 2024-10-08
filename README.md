# Problem 1 - Travelling Salesman & Chinese Postman Problem
  
*blank*
  
# Problem 2 - The Knight's Tour
  
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




