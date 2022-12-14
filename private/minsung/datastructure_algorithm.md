[TOC]

[TOC]

# 자료구조 / 알고리즘

## 선형 자료구조

### 배열

- 메모리 상에 원소를 '연속하게' 배치한 자료구조

#### 배열의 성질

- O(1)에 k 번째 원소를 확인/ 변경 가능

  - Random Access(순서에 상관없이 접근)

- 추가적으로 소모되는 메모리의 양이 거의 없음
- Cache hit rate가 높음
- 메모리 상에 연속한 구간을 잡아야 해서 할당에 제약이 걸림

### 리스트

#### ArrayList(vector)

- 특정 인덱스 값 읽기/수정
  - O(1)
- 삽입
  - 끝 부분
    - 배열의 공간이 남아 있을 경우 O(1)
    - 없을 경우 O(N)
  - 그 외
    - O(N)
- 삭제
  - 끝 부분
    - O(1)
  - 그 외
    - O(N)
- cf : Java의 Vector
  - 동기화됨 -> 매우느린 속도
  - 배열을 복제 할 때 현재 크기의 두 배의 크기의 배열 생성
    - ArrayList는 현재 배열 크기의 절반 크기만을 더한(1.5배) 배열 생성

#### LinkedList

- 메모리가 물리적으로 흩어져 있다.
- k 번째 원소를 확인/변경하기 위해 O(k)가 필요함
- 임의의 위치에 원소를 추가/임의 위치에 원소 제거는 O(1)
- 원소들이 메모리 상에 연속해있지 않아 Cache hit rate이 낮지만 할당이 쉬움
- 종류
  - 단일 연결 리스트
  - 양방향 연결 리스트
  - 이중 연결 리스트
  - 원형 연결 리스트

### 해시테이블

- 키에 대응되는 값을 저장하는 알고리즘

- 특징

  - 충돌이 없을 경우 모든 연산(삽입, 삭제, 탐색, 수정) O(1)

- 해시 함수

  - 임의의 길이의 데이터를 고정된 길이의 데이터로 대응시키는 함수

- 간단한 해시테이블 구현

- 연결 리스트와 해시 코드 함수

- 키의 해시 코드 계산

  - key의 자료형은 보통 int, long
  - 키는 무한하지만 해시 코드는 무한하지 않다.

- 해시 코드를 이용해 배열의 인덱스를 구한다.

  - `hash(key) % array_length`

- 충돌

  - 서로 다른 키가 같은 해시 값을 갖게 될 때 발생

- 충돌 회피

  - Separate Chaining(분리 연결법),

    - 각 인덱스마다 연결 리스트 혹은 트리를 하나씩 둔다.
    - 해시 함수를 제대로 구성하지 못했을 경우, 해시 테이블에 대한 연산의 시간이 O(N)이 된다.

  - Open Addressing(개방 주소법)

    - 인덱스에 바로 (키, 값)쌍 사용
    - 해시 값에 이미 데이터가 존재할 경우(중복) 데이터가 없는 칸을 찾을 때 까지 해시 값에 1을 더한다.
    - 삭제 시 해당 노드를 삭제하면, 그 배열을 비우는 것이 아니라 배열이 삭제 되었다는 쓰레기 값을 따로 둔다.

      - 아래의 나머지 노드들도 탐색하기 위함
      - 삽입 시에는 쓰레기 값이 들어있는 칸에 삽입

    - 클러스터링
      - 해시 배열의 특정 영역에만 데이터가 뭉치는 현상
      - 뭉친 영역의 탐색 비용이 크다.
    - 크기 제한이 있다.

- 균형 이진 탐색 트리를 사용하는 방법도 있음

### 큐

- 한쪽 끝(rear)에 원소를 삽입하고 다른 한쪽 끝(front)에서 원소를 가져올 수 있는 자료구조
- 원소의 추가 O(1)
- 원소의 제거 O(1)
- 제일 앞/뒤의 원소 확인 O(1)
- 제일 앞/뒤가 아닌 나머지 원소들의 확인/변경
  - 원칙적으로 불가능
- 구현 방식

  - 배열

    - 순환 큐

  - 연결 리스트

### 스택

- 한쪽 끝에서만 원소를 삽입하거나 삭제할 수 있는 자료구조.
- 원소 추가 O(1)
- 원소 삭제 O(1)
- 최상단 원소 확인 O(1)
- 최상단 원소가 아닌 나머지 원소들의 확인/변경 불가능(원칙적으로)
- 자바

  - 스택 사용을 지양하라?

    - 동기화된 자료구조(성능)
    - 벡터를 상속받음

  - 보통 **Deque**를 많이 사용한다.
  - 동기화된 자료구조가 필요할 때는 외부 라이브러리를 사용한다.

## 비선형 자료구조

### Graph

- 정점(Vertex)과 정점 두 개를 연결하는 간선(Edge)의 집합
- 방향 그래프
- 무방향 그래프
- Degree
  - 각 정점에 연결된 edge의 개수
  - 방향 그래프에서는 정점을 기준으로 나눈다.
    - Indegree(들어오는)
    - Outdegree(나가는)
- 가중치 그래프
  - 간선에 가중치 정보를 두어서 구성
- 부분 그래프
  - 본래 그래프의 일부 정점 및 간선으로 구성

#### 그래프 표현 방식

- 인접 행렬
  - 두 정점 간의 연결 관계를 O(1)로 확인 가능
  - 공간 O(정점 개수 ^ 2) 차지
  - 밀접한 그래프 용이
- 인접 리스트
  - 연결 리스트 활용
  - 두 정점간의 관계를 선형적으로 확인해야 함
  - 연결 관계가 밀접하지 않은 그래프 표현에 용이
  - 공간 O(E + V)

#### 탐색 방식

- DFS

  - 정점에서 연결된 한 정점만 우선적으로 탐색

  - 정점이 없을 경우 그 전 단계의 정점으로 돌아가서 탐색 지속

  - 스택

  - 순서

    1. 탐색을 시작하는 정점을 방문 체크하고 push

    2. 방문하지 않은 연결된 정점이 있으면 해당 정점을 기준으로 1번 반복

    3. 더 이상 연결된 정점이 없으면 pop하고 pop한 정점을 기준으로 2번부터 과정 반복

- BFS

  - 한 장점에서 연결된 모든 정점을 우선적으로 탐색

  - 순서

    1. 탐색을 시작하는 정점을 enqueue

    2. dequeue한 정점과 연결된 정점들을 enqueue
    3. queue가 빌 때 까지 2번 과정 반복

### Tree

- 그래프 유형 중 하나
  - 간선의 개수 정점의 개수 - 1
  - 모든 정점이 연결되어 있다.
  - 무방향
  - 사이클이 없다.
- 깊이(레벨)
  - 루트 노드로부터 해당 노드까지의 간선의 개수
- 높이
  - 노드에 있는 깊이 중 최댓값
- 리프 노드
  - 자식 노드가 존재하지 않는 부모 노드
- Binary Tree
  - 루트 노드를 중심으로 최대 두 개의 서브트리로 나뉜다.
  - 각 노드의 키 값은 중복되지 않는다.
- Full Binary Tree
  - 정 이진 트리
  - 모든 노드가 리프 노드거나 2개의 자식 노드만 갖는 이진 트리
- Complete Binary Tree
  - 리프 노드를 제외하고 모든 노드의 자식 노드가 왼쪽부터 순서대로 채워져 있는 노드
- Binary Search Tree
  - 노드의 키 값은 유일하다.
  - 왼쪽 자식 노드의 키 < 부모 노드의 키 < 오른쪽 자식 노드의 키
  - 탐색 시간 O(log N) 혹은 O(높이)
  - 편향된 트리가 되어 최악의 경우 O(N)탐색 시간
- 형제 노드
  - 같은 부모를 갖는 노드

### Heap

- 완전 이진 트리
- 최대힙
  - 각 노드의 우선 순위가 자식 노드의 그것보다 크거나 같은 완전 이진 트리
- 우선 순위가 가장 높은 노드 탐색 비용 O(1)
- 삽입/삭제 시 O(logN)
  - 힙의 구조를 유지하기 위해 재정렬 해야 하기 때문
  - 삽입 시에는 새로운 노드를 트리의 가장 끝에 추가한 뒤 재정렬
  - 삭제 시에는 가장 끝에 있는 노드를 루트 노드로 옮긴 뒤에 재정렬

### Trie

- 동적 집합이나 연관 배열을 저장하는 데 사용되는 트리 자료 구조
- 주로 키는 문자열
- 노드 자체와 연관된 키를 저장하지 않다.
  - 노드에서 트리가 차지하는 위치가 연관된 키를 정의
  - 노드의 모든 자손은 노드에 연관된 문자열에 공통 접두사를 공유한다.
- 시간 복잡도
  - 문자열의 길이가 m일 때 O(m)
    - 노드의 개수와 상관이 없다.
- 키 충돌이 일어나지 않는다.
- 해시 함수 필요 없음
- 일반적으로는 해시 테이블보다 느리다.
- 메모리 문제

### AVL Tree

- 자가 균형 이진 탐색 트리의 일종
- 노드의 개수가 n 일 때 탐색, 삽입, 삭제 연산에 대해 log(n) 보장
  - 높이가 일정하도록 최적화하기 때문
- 삽입 과정에서 새로 삽입된 노드에 가장 가까우면서 Balance factor(두 자식 노드의 높이 차)가 2 이상인 조상 노드가 있을 때 회전한다.

### Red Black Tree

- 자가 균형 이진 탐색 트리의 일종
- 각 노드는 색깔이라는 개념을 가진 이진 비트를 보관한다.
- 트리가 수정될 때 마다 새로운 트리는 다시 정렬되고, 새로운 색깔을 갖게 된다.
- 리프 노드를 NIL이라 보른다.
  - 이 노드는 키나 데이터를 갖고있지 않는다.
  - 보통 null 로 표현, 아니면 black 색깔을 가진 특별한 노드로 표현
- black depth
  - 루트 노드로부터 해당 노드까지 도달하는 데 검은 색 노드가 얼마나 있는가
- 붉은 노드는 자식들은 모두 검은 노드다.
  - 따라서 레드 노드가 연이어 나타날 수 없다.
    - 최단 경로 : 노드가 모두 블랙
    - 최장 경로 : 노드가 레드-블랙 순으로 번갈아 등장
- 해당 노드에서 각 NIL 노드로 도달할 때 까지 존재하는 black node의 개수는 같아야 한다.

## 정렬

### 선택 정렬

1. 주어진 리스트 중에 최솟값을 찾는다.
2. 그 값을 맨 앞에 위치한 값과 교체한다.
3. 맨 처음 위치를 뺀 나머지 리스트를 같은 방법으로 교체한다.

### 거품 정렬

- 두 인접한 원소를 검사하여 조건에 맞지 않으면 두 원소의 자리를 교체하는 알고리즘

### 삽입 정렬

- 앞에서 부터 이미 정렬된 배열과 비교해서 자기 위치를 찾아서 삽입해서 정렬을 완료시키는 알고리즘
- 시간 복잡도 최상 : O(n), 최악 : O(n^2)

### 병합 정렬

1. 리스트의 길이가 1 이하이면 이미 정렬된 것으로 파악
2. log(N)
3. 그렇지 않을 경우
   1. 분할 : 정렬되지 않은 리스트를 절반으로 잘라 비슷한 크기의 두 부분 리스트로 나눈다.
   2. 정복 : 각 부분 리스트를 재귀적으로 합병 정렬을 이용해 정렬한다.
   3. 결합 : 두 부분 리스트를 하나의 정렬된 리스트로 합병한다.
   4. 복사 : 임시 배열에 저장된 결과를 원래 배열에 복사한다.

### 퀵 정렬

- 분할 정복(비균등 분할 정복)
  1. 리스트 가운데서 하나의 원소(피벗)을 고른다.
  2. 피벗 앞에는 피벗보다 값이 작은 모든 원소들이 오고, 피벗 뒤에는 피벗보다 값이 큰 모든 원소들이 오도록 리스트를 둘로 나눈다.(분할)
  3. 분할된 두 개의 작은 리스트에 대해 재귀적으로 이 과정을 반복한다. 재귀는 리스트의 크기가 0 또는 1이될 때까지 반복한다.
- 시간복잡도
  - 평균 O(nlog(n))
  - 최악 O(n^2)

### 힙 정렬

1. 최대 힙 트리나 최소 힙 트리로 구성해 정렬을 하는 방법.

2. 정렬되지 않은 리스트로 최대 힙을 구성한 다음에

3. 루트에 위치한 우선순위가 가장 큰 수를 가장 끝에 있는 힙의 원소와 교체하고, 힙의 사이즈를 1 줄인다.
   - 힙의 원소가 하나만 남을 때까지 2,3을 반복한다.
4. 해당 리스트는 정렬되어 있다.



### 불완전 정렬

* 원래 원소가 있던 순서대로 나오지 않는 정렬 알고리즘(입력 순서랑 맞지 않는)
* 값이 같은 원소가 여러 개 있을 때 따져볼 수 있다.
