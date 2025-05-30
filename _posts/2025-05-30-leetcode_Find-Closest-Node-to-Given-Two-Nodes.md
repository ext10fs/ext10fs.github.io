---
layout: single
title: "Find Closest Node to Given Two Nodes(LeetCode)"
categories: CodingTest
tags: [graph]
---

## <a href="https://leetcode.com/problems/find-closest-node-to-given-two-nodes" target="_blank">Find Closest Node to Given Two Nodes</a>

```cpp
int closestMeetingNode(vector<int>& edges, int node1, int node2)
{
    int n=edges.size();
    vector<int> v1(n, -1), v2(n, -1);
    
    int dist=-1, idx=node1;
    while(idx >= 0 && v1[idx] < 0)
        v1[idx] = ++dist, idx = edges[idx];
    
    dist=-1, idx=node2;
    while(idx >= 0 && v2[idx] < 0)
        v2[idx] = ++dist, idx = edges[idx];
    
    int ans = -1, mn = INT_MAX;
    for(int i=0; i<n; ++i)
    {
        if(v1[i]<0 || v2[i]<0)
            continue;

        dist = max(v2[i],v1[i]);
        if(mn > dist)
            mn = dist, ans = i;    
    }
    return ans;
}
```

### 제한 사항

- 2 <= n <= 10^5
- edges[i] != i

### 문제 설명

- 주어진 두 노드(node1, node2)에서 가장 가까운 노드를 찾는 문제
- edges 배열은 각 노드의 다음 노드를 나타내며, -1은 연결이 없음을 의미
- edges[i] != i는 자기 자신으로의 연결이 없음을 보장
- 각 노드의 edge는 단방향 싱글 연결, 순환 구조일수는 있음
- 만약 거리가 같다면 인덱스가 작은 노드를 반환
- 자기자신과의 거리는 0

### 풀이

- 힌트에서는 BFS를 이용하라고 했으나 단방향 싱글 연결이므로 단순 반복문으로 node1, node2와의 거리를 전부 구할수 있음
- 도달할수 없으면 거리는 -1로 표시
- 각 노드에 대해 node1과 node2에서의 거리를 비교하여 둘 중 더 큰 값을 구함
- 그렇게 구한 값들 중 가장 작은 값을 찾은후 반환
- 순환 구조에 대한 예외처리 필요
