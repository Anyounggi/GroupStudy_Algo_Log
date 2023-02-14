# 자료구조 6회차 - 힙, 이진탐색트리

## 𝐈𝐧𝐟𝐨

- 📌 발표자 : [Anyounggi](https://github.com/Anyounggi)
- 🗓️ 2023-02-13



<br><br>


# 트리(tree)

---

- **계층적 관계**를 표현하는 자료구조
- 부모, 자식 노드를 갖고 데이터를 표현한 자료구조
- 자식이 하나만 있거나 없는 경우를 연결리스트
- 자식이 최대 두 개인 트리를 **이진 트리** - 가장 많이 쓰임

<br>

### 트리의 용어

![image](https://user-images.githubusercontent.com/122584209/218399528-0d4c758e-8a1a-4b1c-8dfe-96b91faefbf9.png)


- 노드 : 키를 포함하는 개별 객체
    - 부모 노드 : 나와 직접적으로 연결된 상위노드
    - 형제노드 : 나와 같은 부모를 둔 같은 레벨에 있는 노드
    - 자식 노드 : 나와 직접적으로 연결된 하위 노드
- 엣지(Edge) : 노드를 연결하는 선, 링크라고도 부름
- **루트(root) 노드** : 최고 조상, 트리는 항상 루트에서부터 시작하고 하나만 가짐
- 리프(leaf) 노드 : 자식이 없는 노드
- 레벨 : 루트 노드가 0, 한 세대씩 내려가면서 1씩 증가
- 깊이 : 루트 - 현재 노드의 거리
- 높이 : 현재 노드 - 리프의 거리
  - **트리 높이** : 루트 노드의 높이
- 경로(path) : 두 노드 사이를 연결하는 엣지의 시퀀스
  - A - H의 경로 (A - B - D - H)
  - 경로 길이 : 4
- **크기(size)** : 트리에 포함된 모든 노드의 개수

<br>

## 트리 표현법

### 1. **리스트**

- 부모노드가 자식노드 2개씩 가지고 있다고 가정하고 리스트를 구성, 비어있는 자식 노드는 None으로 처리

<br>

**<아래의 트리 가정했을 때>**

<img width="245" alt="image" src= 'https://user-images.githubusercontent.com/83294376/218629710-6bdab3d9-33cd-4041-9b63-38b3b120d992.png'>



- level 0인 a부터 저장
    - L = [a]
- level 1 중 왼쪽에 있는 b, 그 다음 c 저장
    - L = [a, b, c]
- level 2 중 왼쪽에 있는 노드부터 저장하려고 하는데 **b의 자식이 1개 뿐이기에 None으로 이를 표현**
    - L = [a, b, c, **None, d, e, f**]
- level 3 중 왼쪽에 있는 노드부터 저장하려고 하는데 b의 자식의 자식 노드를 표시해줘야 함으로 None으로 이를 표현
    - L = [a, b, c, None, d, e, f, **None, None, h, i, g** ]

<br>

**리스트 표현법의 장점**

- 비어 있는 곳을 None으로 처리하기 때문에 자식, 부모 노드를 인덱스로 바로 찾아갈 수 있다 → **O(1)**으로 처리 가능
- H[0]
    - 왼쪽 자식 → H[ 0*2 **+ 1** ]
    - 오른쪽 자식 → H[ 0*2 **+ 2** ]
- H[2]
    - 왼쪽 자식  → H[ 2*2 **+ 1** ] = H[5]
    - 오른쪽 자식 → H[ 2*2 **+ 2** ] = H[6]
- H[k]
    - 왼쪽 자식 → H[ k*2 **+ 1** ]
    - 오른쪽 자식 → H[ k*2 **+ 2** ]
    - 부모 노드 → H[ (**k-1) // 2** ]

<br>

**리스트 표현법의 단점** 

- 모든 노드가 다 차있다고 가정하기에 None으로 채움으로써 메모리를 낭비할 수 있다

<br>

## 2. **리스트(재귀적)**

<img width="245" alt="image" src= 'https://user-images.githubusercontent.com/83294376/218630279-30708469-7956-4c30-a146-109c5805fb86.png'>


`[a, [a의 왼쪽 부트리], [a의 오른쪽 부트리]]`를 중첩으로 표현

- [a의 왼쪽 부트리] = `[b, [], [d, [], []]]`
- [a의 오른쪽 부트리] = `[c, [e, [], []], [f, [], []]]`

`[a, [b, [], [d, [], []]], [c, [e, [], []], [f, [], []]]`

[이진트리그리기](http://ironcreek.net/syntaxtree/)

<br>

## 3. 노드 class

- 부모노드와 키값, 자식노드를 직접 표현
- parent, left, right, key(or value)

![image](https://user-images.githubusercontent.com/122584209/218399609-c067c1fb-a36a-4d46-8ddb-6aeb331bbc45.png)


<br>
<br>

# 힙(heap)
* 힙 성질을 만족하는 **이진트리**(자식이 최대 2개)
* **리스트 표현법 사용**

```python
H = [a, b, c, None, d, e, f]
#    ^  ^--^   ^----------^
#    0    1          2
```

- 부모노드, 자식노드의 위치 상수시간에 계산 가능
- None표시가 자리를 차지 → 레벨을 꽉 채워 낭비를 줄임

<br>

### **힙의 조건**

- 모양 성질
    - **레벨 별로 모든 노드가 (None이 없이) 꽉 차있고** 마지막 레벨만 노드가 왼쪽부터 채워져 있는 형태
- 힙 성질
    - **부모 노드의 key 값이 자식노드의 key값보다 커야 한다**
    - 항상 루트 노드에 가장 큰 값이 들어있다

<br>

### **제공연산**
- `insert`
  - O(logn)
- `find_max`
  - O(1)
  - 파이썬의 max( ) 는 O(n)
- `delete_max`
  - O(logn)

> `insert`, `delete_max`는 연산 이후 heap상태를 유지해주는 연산 필요 - O(log n)

<br>

### 특이사항

- search는 비효율적이기 때문에 사용할 이유가 없다
- find_min, delete_min을 만들고 싶다면 min이 제일 위로 오는 힙을 만들면 된다

<br>
<br>

# 힙(heap) 2


## make_heap 구현 
![image](https://user-images.githubusercontent.com/122584209/218399692-926574e5-a78d-4327-9429-932cc6f324bc.png)

위와같이 heap성질을 만족하지 않는 리스트를 힙 성질에 맞도록 변경하는 것을 `make_heap`, 이를 위해 `heapify_down`을 반복적으로 수행

- 부모와 자식들의 값을 비교하며 리프까지 내려가는 연산
- 리스트의 가장 오른쪽 숫자부터 순서대로 진행

1. **제일 마지막 level에 있는 노드부터 비교한 뒤(1, 12, 11) 교환**
![image](https://user-images.githubusercontent.com/83294376/218639618-7aeb47ff-35b7-489f-8dc2-9eb743063cce.png) 
     - [2, 8, 6, 1, 10, 15, 3, 12, 11] → [2, 8, 6, 12, 10, 15, 3, 1, 11]
2. **마지막 level 바로 전의 level, 가장 오른쪽에 있는 노드들을 비교한 뒤(6, 15, 3) 교환** 
![image](https://user-images.githubusercontent.com/83294376/218639675-b5011cfb-393a-47ec-9945-de397d097a7d.png)
      - [2, 8, 6, 12, 10, 15, 3, 1, 11] → [2, 8, 15, 12, 10, 6, 3, 1, 11]
3. **그 왼쪽에 있는 트리의 노드들을 비교한 뒤(8, 12, 10) 교환**
![image](https://user-images.githubusercontent.com/83294376/218639727-471c0136-14d4-430e-a5ea-0ce5c1cba927.png)
      - [2, 8, 15, 12, 10, 6, 3, 1, 11] → [2, 12, 15, 8, 10, 6, 3, 1, 11] 
4. **8의 자식 노드들을 비교한 뒤(8, 1, 11) 교환**
![image](https://user-images.githubusercontent.com/83294376/218639788-16cdcda9-ff16-477c-9c16-c6cd0932d9a6.png)
      - [2, 12, 15, 8, 10, 6, 3, 1, 11] → [2, 12, 15, 11, 10, 6, 3, 1, 8] 
5. **마지막으로 루트 노드를 살펴준다 (2, 12, 15)**
![image](https://user-images.githubusercontent.com/83294376/218639815-aea5e52e-1368-4a47-b49f-8d7f3cff704d.png)
    1. 가장 큰 숫자인 2와 15를 바꾼다
    2. 2가 자신의 자리가 아님으로 2의 자식 노드들을(2, 6, 3) 살펴준다
    3. 해당 트리에 속한 숫자 중 가장 큰 6과 자리를 바꾼다

```python
def make_heep(self, H):
  n = len(self.H)
	
  # 리프노드부터 루트노드까지 비교
  for idx in range(n-1, -1, -1):
    heapify_down(idx, n)  # k = A[k], n = heap원소 개수
```
*  range(n-1, -1, -1) → range(n//2 -1, -1)로 변경해도 마지막 level의 노드 $2^h$개가 리프 노드기에 문제없다

<br>

``` python
def heapify_down(self, idx, n):

  # 전달 받은 노드의 부모와 자식들을 비교했는데 교환하게 된다면
  # 자신 자리가 아닌 자리에 가는 것이기에
  # 리프노드가 나올때 까지 비교해야 한다
  while 2*idx + 1 < n:

	  L = 2 * idx + 1 # 왼쪽 자식
	  R = 2 * idx + 2 # 오른쪽 자식
		
	  # 왼쪽 자식이 부모보다 크다면
	  if self.H[L] > self.H[idx]:
	    m = L # m = max val idx
	  else:
	  	m = idx

	  # 오른쪽 자식이 부모보다 크다면
	  # 오른쪽 자식의 out of range 가능성 방지
	  if R < n and self.H[R] > self.A[m]:
    	m = R # m = max val idx
	  else:
    	m = idx

    # 부모(idx)와 자식이 다르면 교환
    if m != idx: 
    	self.H[idx], self.H[m] = self.H[m], self.H[idx]
      idx = m
    # 부모가 제일 크다면 더 내려갈 이유가 없다
    else:
      break
```
- w.c는 루트부터 가장 깊은 레벨의 리프까지 내려가는 것



<br>

pseudo code (축약해서 이해)
```python
heapify_down(k, n):
  # H[k], n값
  while H[k] != leaf:
    L, R = 2*k+1, 2*k+2
    m = index_max(H[k], H[L], H[R]) # 셋중에 가장 큰 인덱스
    if k != m:
      # H[K], H[m]을 swap
    else:
      break
```

<br>

### 성능

* `make_heap` 은 `heapify_down` 을 n번 호출

* `heapify_down` 의 최악의 입력은 root에서 최하단 leaf까지 → 높이(h)

* `make_heap` O(nh)

<br>

- `heapify_down`: O(h) = O(log N)
- `make_heap`: o(nh) = O(n log N) (리프 노드부터 가는걸 생각하면 O(n) 시간에도 된다는걸 알 수 있다)

<br>

### n개의 노드를 가진 힙의 높이

n개의 노드를 가진 힙의 높이 h는?

- level 0 : 1
- level 1 : 2
- level 2 : $2^2$
- level 3 : $2^3$
- level h - 1 : $2^{h-1}$
- level h : < $2^h$

<br>

- 마지막 level 이전까지의 노드 개수
    - $1 + 2^1 + 2^2 + 2^3 + 2^4 + 2^5 + 2^6 ... + 2^{h-1}$
- 마지막 level의 노드 개수 (= 최소 1개) $< 2^h$
- $1 + 2^1+ ... + 2^{h-1}$ **+ 1** ≤ n (n개의 노드를 가진 힙)
    - $1 + 2^1+ ... + 2^{h-1}$ = $2^h - 1 / 2 - 1$
- ($2^h - 1 / 2 - 1$ + 1 = ) $2^h$  ≤ n
- **최종** : $log_2^{2^h}$ ≤ $log_2^n$ → h ≤ $log_2^n$


<br><br>

# 힙(heap) 3

## insert 구현
- 삽입 연산 이후로도 힙 성질을 만족해야 한다
- heapify_up 연산 사용
    - 부모와 자식의 값을 비교하며 루트노드까지 올라가는 연산

`A = [15,12,6,11,10,2,3,1,8]` 에 append(14) 하면 heap성질 만족 X

```
          15
        /    \
      12       6
     /  \    /   \
    11   10 2      3
   / \   /
  1   8 14
```

14의 부모노드인 10과 자리를 변경, 바뀐 자리에서 부모노드인 12와 변경하면 힙 성질 만족한다

![image](https://user-images.githubusercontent.com/83294376/218644643-6f0894fd-2a27-41b1-a9b6-4fd9993c8f31.png)

<br>

`heapify_up`
- 부모노드로 가면서 자신의 자리를 찾도록 만드는 함수

```python
def insert(self, val):
  self.H.append(val)
  self.H.heapify_up(len(self.H))
  # append 했기에 마지막 리프노드부터 
  # root 방향으로 이동하면서 heapify up을 해준다.


def heapify_up(self, target_idx):
  child_idx = (target_idx - 1) // 2
  
  # 루트에 도달하지 않았고
  # 부모의 값이 자식보다 작으면
  while idx > 0 and self.H[child_idx] < self.H[idx]:
    self.H[target_idx] , self.H[child_idx] = self.H[child_idx], self.H[target_idx]

    # 자식의 인덱스를 다시 메인 인덱스로 만든다
    target_idx = child_idx
```

<br>

### find_max 구현

```python
find_max:
  return A[0] # O(1)
```

힙성질을 만족하고 있으면 루트노드가 가장 큰 수

<br>

### delete_max 구현
- 가장 큰 값을 삭제
- 가장 마지막 리프 노드가 임의로 루트 노드 자리를 채운다
- `heapify_down(0, n)` 통해 root부터 재정리
  - O(logN)

```python
def delete_max(self):
  if len(H) == 0:
    return None

  key = self.H[0]
  self.H[0] = self.H[len(self.H)-1]
  self.H.pop()
  self.H.heapify_down(0, len(self.H))

  return key
```

<br>

### 성능

- `heapify_up`
  - O( h(level) ) → O(logN)
- `insert` 구현
  - O(logN)
  - n개의 숫자를 insert해서 힙을 만들면 O(NlogN)
- `find_max`
  - O(1)
- `delete_max` 
  - O(logN)

힙은 정리가 되어있지 않아 모두 탐색해야 하므로, `search`함수를 지원하지 않음

<br>

### heap sort 구현
- 힙이 아니라면 `make_heap`으로 힙을 만든 후, `heapify_down` 함수를 반복 적용하여 값들을 오름차순으로 재배치하는 함수
  - `make_heap`으로 기존의 리스트를 정렬시킨다면 O(N)
  - `heap_sort`를 사용한다면 O(NlogN)
```python
def heap_sort(self): 
  n = len(self.H)

  for idx in range(len(self.H)-1, -1, -1): 
    self.H[0], self.H[idx] = self.H[idx], self.H[0]
		
    # 마지막 리프는 루트의 값이 들어갔음으로 제외하고 
    # 힙으로 다시 만들어준다
    n = n - 1
    self.heapify_down(0, n)
```

<br>

## Python에서의 힙

- **min-heap**을 사용한다! 우리는 max-heap을 다루었다
    - `import heapq` 필요
    - 리스트를 사용
<br>

**지원연산**
- `heappush(h, key)`
    - 힙 h에 key값을 삽입(=insert와동일)
    - heappush(h, (key, value))처럼 튜플 삽입 가능
- `heappop(h)`
    - 최소값을 지우고 리턴 (delete_min)의 역할
- `heapify(A)`
    - 리스트 A를 힙 성질이 만족되도록 변환
    - make_heap()과 동일 (단, min-heap으로 변환)
- h[0]: 힙의 최소값을 알고 싶다면 사용

<br><br>

# 적응형 힙(Adaptive Heap)

- 최소 혹은 최대의 값만을 제거할 수 있는 기존의 힙에서 **특정한 값을 찾아 제거**할 수 있도록 하는 힙
- 힙은 그대로 둔 채 **값만 바꿀 수 있게** 하는 힙


<max-heap으로 구현>

```python
class AdapedHeap:

  # key 값과 key 값이 저장된 인덱스를 담는 중첩 클래스 선언
  class Locator:
    def __init__(self, key, l_idx):
      self.key = key 
      #(optional) self.value = value 
      self.l_idx = l_idx


  def __init__(self):
    self.H = []


  def __str__(self):
    return str(self.H)


  def __len__(self):
    return len(self.H)


  def insert(self, key)
    # 저장하고 싶은 값과 저장될 인덱스를 locator로 전송
    # append 하기에 끝 인덱스 전송
    loc = self.Locator(key, len(self.H))
    # 삽입
    self.H.append(loc)
    # loc이 저장된 곳에서 부터 올라가며 교환 
    self.heapify_up(loc.l_idx)
    return loc


  def heapify_up(self, main_idx):
    parent_idx = (main_idx - 1) // 2

    # out of range 방지
    # 자식 위에 부모가 존재하고, 자식이 부모보다 더 클 경우
    if parent_idx > 0 and self.H[parent_idx].key < self.H[main_idx].key:
      # key 교환
      self.H[main_idx].key, self.H[parent_idx].key = self.H[parent_idx].key, self.H[main_idx].key
      # 인덱스 교환 
      self.H[main_idx].l_idx = main_idx
      self.H[parent_idx].l_idx = parent_idx

      # 부모를 다시 main으로 비교, 교환하는 재귀적 연산 수행
      heapify_up(self, parent_idx) 


  def heapify_down(self, main_idx, h_len):
    # len_h = 전달된 len(self.H)
    L_idx = self.H[main_idx]*2 + 1
    R_idx = self.H[main_idx]*2 + 2 

    # 왼쪽 자식이 out of range가 아니고
    # 부모보다 더 큰 값이라면
    if L < h_len and self.H[L_idx].key > self.H[main_idx].key:
      # key 교환
      self.H[L_idx].key, self.H[main_idx].key = self.H[main_idx].key, self.H[L_idx].key
      # 인덱스 교환 
      self.H[L_idx].l_idx = L_idx
      self.H[main_idx].l_idx = main_idx

      # 바뀐 자리는 자기 자리가 아님으로 다시 비교후 교환하는 재귀적 연산 수행
      heapify_down(self, L_idx)
		

    # 오른쪽 자식이 out of range가 아니고
    # 부모보다 더 큰 값이라면
    if R_idx < h_len and self.H[R_idx].key > self.H[main_idx].key:
      # key 교환
      self.H[R_idx].key, self.H[main_idx].key = self.H[main_idx].key, self.H[R_idx].key
      # 인덱스 교환 
      self.H[R_idx].l_idx = R_idx
      self.H[main_idx].l_idx = main_idx

      # 바뀐 자리는 자기 자리가 아님으로 다시 비교, 교환하는 재귀적 연산 수행
      heapify_down(self, R_idx)		
```

* loc 자체를 swap하는게 아니라 key와 value를 따로 따로 swap하는 이유 
  * loc 자체를 swap할 경우 어차피 인덱스를 따로 교환해줘야 하기 때문. 
  * 해당 코드에서는 코드의 명료함을 위해 따로따로 swap 하였다.

<br>

## A-Heap의 remove 구현

- key가 아닌 locator 전달하여 인덱스로 값을 바로 찾아가 제거할 수 있다
- 특정한 값을 찾아 제거할 수 있도록 하는 연산
- O(logN)
    - w.c - 루트의 값 제거 하는 경우
    - 루트부터 한 길을 따라 리프까지 재정렬

```python
def remove(self, loc):
  target_idx = loc.l_idx

  # 전달된 loc이 우리가 선언한 힙 안에 없다면 에러 발생
  if not(0 <= target_idx < len(self.H) and self.H[target_idx] == loc):
    raise ValueError('Invalid locator')

  # 삭제할 loc을 제일 끝 리프와 교환
  # 리프로 이동한 노드는 어차피 삭제할 것이기에 인덱스 조정 필요없다
  self.H[target_idx].key, self.H[-1].key = self.H[-1].key, self.H[target_idx].key
  self.H[target_idx].l_idx = target_idx
	
  # 마지막 노드 제거
  self.H.pop()

  # 교환된 자리가 자기 자리가 아님으로 다시 비교, 교환
  heapify.down(self, target_idx)
```

<br>

## A-Heap의 delete_max 구현

- O(logN)
    - 루트부터 한 길을 따라 리프까지 재정렬

```python
def delete_max(self):
	self.H.remove(self.H[0])
```
<br>

## A-Heap의 heap_sort 오름차순 구현

- O(NlogN)
    - w.c를 고려했을때 NlogN

```python
def heap_sort(self):
	h_len = len(self.H)
	for target_idx in range(len(self.H), -1, -1):
		# heapify_down, heapify_up 둘다 max_heap으로 구현하였기에 루트가 가장 크다
		# key 교환
		self.H[0].key, self.H[target_idx].key = self.H[target_idx].key, self.H[0].key

		# 인덱스 교환
		self.H[0].l_idx = 0
		self.H[target_idx].l_idx = target_idx
		
		# 루트가 가장 마지막으로 갔음으로 이 노드는 고정시킨다
		h_len = h_len - 1
		self.heapify_down(0, h_len)
```

`update_key`

- 기존의 힙에서 insert를 하면 제일 마지막 리프에 추가해서 heapify_up 사용했지만
- update_key는 목표로 하는 노드의 key값을 바꿔준다
- O(logn)
    - w.c - 루트에 더 작은 key가 들어오는 경우

```python
def update_key(self, target_loc, new_key):
	target_idx = target_loc.l_idx 
	
	# key를 바꾸려는 loc이 해당 힙에 존재하지 않는다면 에러 발생	
	if not(0 <= k <len(self.H) and self.H[target_idx] == loc):
		raise ValueError('Invalid locator')

	# 새로 들어오는 키가 더 작다면 아래로 가야한다
	# max-heap을 만들고 있기에 작은 노드가 리프에 있어야 한다
	if target_loc.key > new_key:
		target_loc.key = new_key
		self.heapify_down(target_idx) 

	# 새로 들어오는 키가 더 크다면 위로 가야한다
	# max-heap을 만들고 있기에 큰 노드가 루트에 있어야 한다
	elif target_loc.key < new_key:
		target_loc.key = new_key
		self.heapify_up(target_idx)
```
<br><br>

# 이진트리(Binary tree)

이진트리의 표현법

- 리스트, Node class를 이용한 표현

```python
class Node:
  def __init__(self, key):
    self.key = key
    self.parent = self.left = self.right = None
  def __str__(self):
    return str(self.key)
```

```python
a = Node(6)
b = Node(9)
c = Node(1)
d = Node(5)
a.left = b
a.right = c
b.parent = c.parent = a
b.left = d
d.paren = b
```

```
    6
   / \
  9   1
   \
    5
```

### 이진트리 순회(traversal)

```
       F
     /   \
   B       G
  / \       \
 A   D       I
    / \     /
   C   E   H
```

1. **전위 순회(Preorder)**

- **노드 → 왼쪽 서브트리 → 오른쪽 서브트리** 순으로 순회
- F B A D C E G I H

1. **중위 순회(Inorder)**

- **왼쪽 서브트리 → 노드 → 오른쪽 서브트리** 순으로 순회
- A B C D E F G H I

1. **후위 순회(Postorder)**

- **왼쪽 서브트리 → 오른쪽 서브트리 → 노드** 순으로 순회
- A C E D B I H G F

[자료구조 트리](https://velog.io/@kimdukbae/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-%ED%8A%B8%EB%A6%AC-Tree#%ED%8A%B8%EB%A6%ACtree-%EC%88%9C%ED%9A%8C)

```python
class Node:
  def __init__(self, key):
    self.key = key
    self.parent = self.left = self.right = None

  def __str__(self):
    return str(self.key)

  def preorder(self): #현재 방문중인 노드 == self
    if self != None: # MLR
      print(self.key)
      if self.left:
        self.left.preorder()
      if self.right:
        self.right.preorder()

  def inorder(self):
    if self != None: # LMR
      if self.left:
        self.left.inorder()
      print(self.key)
      if self.right:
        self.right.inorder()

  def postorder(self):
    if self != None: # LRM
      if self.left:
        self.left.postorder()
      if self.right:
        self.right.postorder()
      print(self.key)
```

# 이진탐색트리(Binary Search Tree)

---

특정 키 값을 찾는 연산을 효율적으로 할 수 있도록 조직화한 트리

이진탐색트리의 정의

- `left node`의 key 값은 부모 노드의 key 값보다 작은 값
- `right node`의 key 값은 노드의 key 값보다 큰

```
      15
    /    \
   4      20
  /      / \
 2     17   32
        \
        19
```

- 일반적으로 리스트의 검색은 O(N), 이진 탐색 트리는 O(log N)
- 리스트보다 이진 탐색 트리의 검색이 훨씬 효율적

### 탐색

`find_loc()`

```python
def find_loc(self, key):
  if self.size == 0: return None
  p = None # p is parent of v
  v = self.root

  while v != None:
    if v.key == key: return v
# v보다 크면 오른쪽 서브트리
    elif v.key < key:
      p = v
      v = v.right
# v보다 작으면 왼쪽 서브트리
    else:
      p = v
      v = v.left
  retern p
```

`search()`

```python
def search(self, key):
  v = self.find_loc(key)
  if v == None:
    return None
  else:
    return v
```

### 삽입

`insert` : O(log n)

```python
def insert(self, key):
  p = self.find_loc(key) # find_loc() 은 O(h)

  if p == None or p.key != key:
    v = Node(key)
    if p == None:
      self.root = v
    else:
      v.parent = p

      if p.key >= key: # left/right
        p.left = v
      else:
        p.right = v
    self.size = self.size + 1
    return v

  else:
    print("key is already in the tree")
    return p # 중복 key를 허용하지 않으면 None 리턴 p == None
```

# 이진탐색트리 연산

---

- deleteByMerging
- deleteByCopying

### **deleteByMerging**

x노드를 지울 때, x 자리에 L을 두고, L의 가장 큰 노드의 자식 노드로 R을 연결

![image](https://user-images.githubusercontent.com/122584209/218384325-501b544c-aa52-4593-88a6-350bffd5faa1.png)

경우들

1. 왼쪽 자식노드가 없는 경우 : R로 x 대체
2. x가 루트인 경우 : 루트 노드를 업데이트

```python
deleteByMerging(self, x): # 노드 x 를 삭제
  a = x.left, b = x.right
  pt = x.parent
  # c == x를 대체할 노드
  # m == L에서 가장 큰 노드

# 왼쪽 노드 a가 x를 대체
  if a != None:
    c = a
    m = a

# 왼쪽 노드에서 가장 큰 수
# 오른쪽 노드가 큰 수 이므로 오른쪽끝까지 내려감
    while m.right:
      m = m.right    

# R은 L의 가장 큰 수에 연결
    if b != None:
      b.parent = m
      m.right = b
  else: # a == None (경우1)
    c = b

# 경우 2 : 
  if pt != None:
    if c: c.parent = pt
    if pt.key < c.key:
      pt.right = c
    else:
      pt.left = c

  else:  # pt == None (root == x)
    self.root = c
    if c: c.parent = None
  self.size -= 1
  # return은 다음에 다시 설명하기로 함
```

### deleteByCopying

x 노드를 지울 때, x를 지우는 게 아니라 값을 복사하는 방식으로 변경. L에서 가장 큰 값(m)을 찾아서 x에 복사. 원래 m의 자리에는 m의 왼쪽 서브트리가 들어감

```python
deleteByMerging(self, x): # 노드 x 를 삭제
  a = x.left, b = x.right
  pt = x.parent
  # c == x를 대체할 노드
  # m == L에서 가장 큰 노드

# 왼쪽 노드 a가 x를 대체
  if a != None:
    m = a
# 왼쪽 노드에서 가장 큰 수
# 오른쪽 노드가 큰 수 이므로 오른쪽끝까지 내려감
    while m.right:
      m = m.right   
        c = m
        m.parent = pt

# R은 L의 가장 큰 수에 연결
    if m.left != None:
            a.right = m.left
      m.left.parent = a

  else: # a == None (경우1)
    m = b
        while m.left:
      m = m.left
        c = m
```

### 삭제 연산의 수행 시간

- deleteByMerging: O(h)
- deleteByCopying: O(h)

두 함수 모두 m을 찾는데 드는 수행시간이 대부분을 차지

- insert
- search(find_loc)
- delete(merging, copying)

위 함수 모두 O(h) 시간이 든다.

```python
# case 1
insert(1)
insert(2)
insert(3)
insert(4)

# case 2
insert(2)
insert(1)
insert(3)
insert(4)
```

- 같은 키 값을 저장해도 height의 크기가 다를 수 있음
- 이진탐색트리에서 높이는 수행시간을 결정

어떤 순서로 키값이 삽입되는지와 상관없이 가능하면 작게 유지 → 균형이진탐색트리(balanced binary…)
QnA
