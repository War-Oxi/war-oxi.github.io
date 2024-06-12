---
title: Priority queue 개념, 사용방법
date: 2024-06-12 20:31:44 +0900
author: kkamji
categories: [CS, Data Structure]
tags: [priority queue, python, heap, heapq]     # TAG names should always be lowercase
comments: true
# image:
#   path: https://github.com/kkamji98/kkamji98.github.io/assets/72260110/c39504c6-3de4-4b41-919b-5ef1b132106c
---

Priority queue(우선순위 큐)는 각 요소가 우선순위를 가진 구조로, 항상 우선순위가 높은 요소가 먼저 나가는 데이터 구조입니다. Python에서는 `heapq` 모듈을 사용하여 우선순위 큐를 쉽게 구현할 수 있습니다.

## Priority queue의 개념

Priority -  각 요소는 우선순위를 가지고 있으며, 큐에서 요소를 제거할 때 항상 우선순위가 가장 높은 요소가 먼저 제거됩니다.

Heap - 완전이진 트리의 일종으로 여러 개의 값들 중에서 최댓값 혹은 최솟값을 빠르게 찾기위해 만들어진 데이터 구조입니다. 이러한 특성을 기반으로 우선순위 큐는 보통 `Min-Heap`이나 `Max-Heap`으로 구현됩니다.  

Min-Heap - 가장 작은 요소가 루트에 위치하며 부모 노드의 값이 항상 자식 노드의 값보다 작습니다.

Max-Heap - 가장 큰 요소가 루트에 위치하며 부모 노드의 값이 항상 자식 노드의 값보다 큽니다.

> Heap 데이터 구조에서 같은 Level에 존재하는 노드의 위치는 중요하지 않습니다.  
{: .prompt-info}

## 주요 함수

> Python의 `heapq` 모듈은 리스트를 최소 힙으로 변환하여 우선순위 큐 기능을 제공합니다. `heapq` 모듈은 `Min-Heap`만 지원하므로, `Max-Heap`을 구현하려면 요소의 우선순위를 음수로 저장하는 방법을 사용합니다.  
{: .prompt-info}

- `heapq.heappush(heap, item)`: 요소를 힙에 추가
- `heapq.heappop(heap)`: 힙에서 가장 작은 요소를 제거하고 반환
- `heapq.heapify(list)`: 리스트를 힙으로 변환
- `heapq.heappushpop(heap, item)`: 힙에 요소를 추가한 후, 가장 작은 요소를 제거하고 반환
- `heapq.heapreplace(heap, item)`: 힙에서 가장 작은 요소를 제거한 후, 새로운 요소를 추가

## Min-Heap을 사용한 우선순위 큐 구현

```python
import heapq

# 빈 리스트를 힙으로 사용
priority_queue = []

# 요소 추가
heapq.heappush(priority_queue, (2, "task 2"))
heapq.heappush(priority_queue, (1, "task 1"))
heapq.heappush(priority_queue, (3, "task 3"))

# 요소 제거
while priority_queue:
    priority, task = heapq.heappop(priority_queue)
    print(f"Priority: {priority}, Task: {task}")

# 출력:
# Priority: 1, Task: task 1
# Priority: 2, Task: task 2
# Priority: 3, Task: task 3
```

## Max-Heap을 사용한 우선순위 큐 구현

```python
import heapq

# 빈 리스트를 힙으로 사용
priority_queue = []

# 요소 추가 (우선순위를 음수로 저장)
heapq.heappush(priority_queue, (-2, "task 2"))
heapq.heappush(priority_queue, (-1, "task 1"))
heapq.heappush(priority_queue, (-3, "task 3"))

# 요소 제거
while priority_queue:
    priority, task = heapq.heappop(priority_queue)
    print(f"Priority: {-priority}, Task: {task}")

# 출력:
# Priority: 3, Task: task 3
# Priority: 2, Task: task 2
# Priority: 1, Task: task 1

```

---
> **궁금하신점이나 추가해야할 부분은 댓글이나 아래의 링크를 통해 문의해주세요.**  
> **Written with [KKam.\_\.Ji](https://www.instagram.com/kkam._.ji/)**
{: .prompt-info}
