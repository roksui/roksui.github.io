---
title: "Hashing"
use_math: true
---

### Recall Map
맵은 키-밸류 pair (i.e. items or entries)의 컬랙션이다.
맵의 주요 연산으로는 탐색, 삽입, 삭제 등이 있고, 중복 키가 있을 수 없다.

기본적인 연산에 더해 다음과 같은 메소드도 있을 수 있다.
- entries(): return an iterable collection of the entries in map
- keys(): return an iterable collection of the keys in map
- values(): return an iterable collection of the values in map

### Simple List-Based Map
unsorted list로 맵을 구현할 수 있다.
맵의 아이템들을 임의의 순서로 doubly-linked list에 저장할 수 있다.

--이미지 추가--

성능을 고려해보면, put의 경우, 만약 키가 아직 존재하지 않는다면 시작이나 끝에 추가하면 되므로 O(1)이 된다.
만약 주어진 키가 있다면, put, get remove 모두 최악의 경우 리스트 전체를 traverse해야 하므로 O(n)이 된다.

unsorted list로 구현한 맵은 작은 크기의 맵이나, put이 가장 흔한 연산이고, 탐색과 삭제가 거의 안쓰일 때 사용하는 것이 효과적이다. (예. 어떤 시스템에 대한 로그인 기록을 저장할 때)

### Restricted Keys
키를 주소 형태로 사용해서 아이템을 가져올 수도 있다. 예를 들어, n개의 아이템을 가지는 맵이 있고, 키값이 어떠한 N >= n 에 대해 [0, N - 1]의 값을 가진다고 생각하자.
크기 N의 배열을 만들어 index가 키값이 되어 아이템에 접근할 수 있다. 이렇게 되면, get, put, remove 모두 O(1)이 된다.
하지만, 단점은 대체로 N >> n 이기 때문에, 공간 낭비가 크고 String 키값은 가질 수 없다. 예를 들어, 학생ID가 9자리 숫자면, 키를 보관하기 위해 1,000,000,000 크기의 배열을 만들어야 한다. (실제 학생수 보다 훨씬 큰 숫자가 쓰임)

### Hash Function/Hash Table
이러한 문제점들을 극복하고자 우리는 hash function h를 사용하여 키값과 그에 대응하는 배열 A에 있는 index와 매핑을 할 수 있다.
이때, h는 특정 input x에 대해 항상 같은 output을 주는 수학적 함수이다.
우리는 이러한 자료구조를 hash table이라고 부른다.

--이미지 추가--

좀 더 엄밀히 말해, hash function h는 주어진 타입을 가지는 키값을 fixed한 인터벌 [0, N - 1]의 정수와 매핑해주는 함수이다.

예를 들어, $h(x) = x \bmod N$은 정수 키값에 대한 hash function이다. 여기서 $h(x)$는 키값 x에 대한 hash value라고 한다.

주어진 키 타입 K에 대한 hash table은 다음을 갖는다.
- Hash function $h: K \rightarrow [0, N - 1]$
- Array (table) of size $N$
- Item $(x, o)$ is stored at $A[h(x)]$

### Choice of Hash Function
좋은 hash function을 정하는 것은 항상 쉽지만은 않다.
Hash function h는 주로 두 개의 함수를 합성한 것이다.
1) Hash Code: h1: keys -> integers
2) Compression function: h2: integers -> [0, N - 1]
$h(x) = h_{2}(h_{1}(x))

Hash function을 선택할 때, 되도록이면 많은 아이템들이 한 곳으로 몰리는 현상을 피하고자 한다. 랜덤하게 분배되는 것이 이상적이다.

Hash function을 설계할 때, 두 가지 일반적인 접근법이 있다.
1) 키값 k를 정수 튜플 (x1, x2, ..., xd)로 설정 (각 정수는 어떠한 M에 대해서 [0, M - 1] 값이 됨)
2) 키값 k를 (가능한 매우 큰) nonnegative 정수로 설정

- Summing components
k = (x1, x2, ..., xd)일 때, 다음과 같은 해시 함수를 쓸 수 있다.
$h(k) = $\sum x_{i}$
$h(k) = $\sum x_{i} \bmod p$
$h(k) = $\bigoplus x_{i} \bmod p$

하지만, 키 튜플의 여러 가지 다른 permutation에 대해 같은 해시 값을 가질 수도 있어서 문제가 될 수 있다.
예. "mate", "tame", "meat", "team"은 모두 같은 값으로 매핑된다.

이를 해결하기 위해 다음과 같은 함수를 쓸 수도 있다.
$h(k) = x_{1}a^{d-1} + x_{2}a^{d-2} + ... + x_{d-1}a + x_{d}$

이렇게 되면 동일한 튜플에 대한 permutation 두 가지가 있더라도 충돌하지 않는다.

- Modular division
양의 정수의 키값 k에 대해 사용할 수 있다.
$h(k) = k \bmod N$, for some prime number $N$

만약 M >> N 일때, 키값들이 [0, M] 안에서 randomly uniformly distributed된다면, 두 개의 키값이 충돌하는 확률은 1/N이다.
보통 키값들은 주로 randomly distributed하지 않다.

- Universal hash functions

키값의 범위가 [0, M]이고, [0, N - 1]의 범위를 가지는 해시 함수가 필요하다고 가정해보자.

H가 그러한 해시 함수의 family라고 하자. 만약 H에서 uniformly at random(UAR)하게 h를 뽑아서,

$Pr[h(i) = h(j)] \leq frac{1}{N}$ 이 충족되면, H가 2-universal하다고 한다.

즉, n개의 키값에서 주어진 k에 대한 예상 충돌 수는 최대 n/N이 된다.

- Random Linear Hash Function
양의 정수의 키값 k에 대해 사용할 수 있다.
$h(k) = ((ak + b) \bmod p) \bmod N$, for some prime number $p$, and $a$ and $b$ are chosen uniformly at random from $[1, p - 1]$ with $a \neq 0$.

만약 키값들이 [0, M] 범위 안에 있고 p > M이면, 두 개의 키가 충돌할 확률은 1/N이다.

- Collision Handling
충돌(Collision)은 두 개 이상의 키값이 배열의 같은 아이템으로 해싱되면 발생한다.
좋은 해시 함수에서는 이러한 충돌이 매우 드물다.
만에하나 충돌이 발생하는 것을 대비하여 우리는 이를 처리할 수 있는 방안이 있어야 한다.

- Separate chaining
배열의 각 위치가 entries를 저장하는 linked list를 가르킨다.
get, put, remove 연산은 리스트한테 위임된다.
Separate chaining은 간단하지만 테이블 밖에서 추가적인 메모리가 필요하다.

--코드 추가--

--이미지 추가--

우리의 해시 함수가 n개의 키값들을 [0, N - 1] 범위에 있는 독립적인 uniform random한 값들로 매핑한다고 가정해보자.

배열 A에 있는 버킷으로 매핑되는 아이템의 개수를 random variable X라고 하면, $E(X) = frac{n}{N}$이 된다.
여기서 n/N은 해시 테이블의 load factor라고 하고, 주로 $\alpha$로 쓴다.

충돌이 separate chaining으로 처리된다면, 해시 테이블 연산에 대한 예상 시간은 $O(1 + \alpha)$이다.
하지만, 모든 아이템들이 똑같은 chain으로 충돌할 경우(최악의 경우), 시간 복잡도는 $O(n)$이 된다.

--이미지 추가--

- Open addressing using Linear Probing
Open addressing: 충돌하는 아이템을 테이블의 다른 위치에 넣는다.
Linear Probing: 충돌하는 아이템을 다음 가능한 공간에 넣음으로써 충돌을 처리한다.
이 때, 검사된 각 테이블 cell을 probe라고 하는데, 충돌하는 아이템들이 점점 한 부분에 쌓여, probe들의 시퀀스가 길어진다.

--예시 추가--
--이미지 추가--

