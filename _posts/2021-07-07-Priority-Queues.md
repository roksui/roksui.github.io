---
title: "Priority Queues"
use_math: true
---

## Priority Queue ADT
맵 ADT의 종류로써 키-값 아이템 컬렉션을 저장하고, 가장 작은 키만 삭제할 수 있다.
- insert(k, v): k의 키와 v의 값을 가지는 아이템을 넣는다.
- remove_min(): 가장 작은 키를 가진 아이템을 삭제하고 리턴한다.
- min(): 가장 작은 키를 가진 아이템을 리턴한다.
- size(): 보관된 아이템의 개수를 리턴한다.
- is_empty(): 큐가 비어있는지 확인한다.

min 대신에 max 버전도 있지만 동시에 사용할 수는 없다.

응용 예시: 모던 주식 거래 시스템은 matching engine으로 알려지는 시스템을 핵심으로 한다. 이 엔진은 구매자와 판매자의 주식 거래를 매칭시켜준다.
구매자는 특정 주식을 사기 위한 가격 제시를 할때 특정한 가격에, 혹은 그 이하를 제시한다.
판매자는 특정 주식을 특정 가격에 팔기 위한 제시를 한다.
이 때, 구매 오더와 판매 오더는 각각 priority queue에 저장되고 가격이 가장 높은 우선순위를 갖게 된다. 매칭 엔진은 새로운 오더가 들어올 때 마다 거래가 바로 성사될 수 있는지 확인하고, 그렇다면 거래를 하고 아니면 오더를 큐에 넣어준다.

### Sequence-based Priority Queue
정렬되지 않은 리스트, 혹은 정렬된 리스트로 priority queue를 구현할 수 있다.
정렬되지 않은 리스트는 insert: O(1), remove_min/min: O(n) 시간 복잡도를 가지는 반면, 정렬된 리스트는 insert: O(n), remove_min/min: O(1) 시간 복잡도를 가진다. 둘 다 size/is_empty는 O(1)을 가진다.

우리는 priority queue를 써서 키 리스트를 정렬할 수 있다.
1. 빈 priority queue에 키를 반복적으로 넣어준다.
2. 반복적으로 remove_min을 하여 정렬한다.

{% include pseudocode.html id="1" code="
\begin{algorithm}
\caption{Priority Queue Sort}
\begin{algorithmic}
\PROCEDURE{PQSORT}{$A$}
    \STATE $pq$ $\leftarrow$ new priority queue
    \STATE $n$ $\leftarrow$ size$(A)$
    \FOR{$i = 0$ \TO $n$}
        \STATE $pq$.insert$(A[i])$
    \ENDFOR
    \FOR{$i = 0$ \TO $n$}
        \STATE $A[i] \leftarrow pq$.removeMin()
    \ENDFOR
\ENDPROCEDURE
\end{algorithmic}
\end{algorithm}
" %}

위 슈도코드를 sequence-based priority queue로 구현하면 두 가지 경우 다 O(n^2) 시간 복잡도를 가진다.

### Selection Sort (선택정렬)
정렬되지 않은 리스트 구현의 변형으로 볼 수 있다.
1. n번의 insert 연산은 O(n)을 가진다.
2. n번의 remove_min 연산은 O(n^2)을 가진다.

{% include pseudocode.html id="2" code="
\begin{algorithm}
\caption{Selection Sort}
\begin{algorithmic}
\PROCEDURE{SELECTIONSORT}{$A$}
    \STATE $n$ $\leftarrow$ size$(A)$
    \FOR{$i = 0$ \TO $n$}       
        \STATE $s$ $\leftarrow i$
        \COMMENT{find $s \geq i$ minimizing $A[s]$}
        \FOR{$j = i$ \TO $n$}
            \IF{$A[j] < A[s]$}
                \STATE $s \leftarrow j$
            \ENDIF
        \ENDFOR
        \STATE $A[i]$, $A[s] \leftarrow A[s]$, $A[i]$
        \COMMENT{swap $A[i]$ and $A[s]$}
    \ENDFOR
\ENDPROCEDURE
\end{algorithmic}
\end{algorithm}
" %}

이때, 우리는 top level loop의 invariant는 다음과 같다고 말할 수 있다:
- A[0, i)는 정렬되있다.
- A[i, n)은 priority queue이고 모든 원소가 A[i-1]보다 크거나 같다.

예시:
![Selection Sort]](/assets/img/selection_sort.png)

### Insertion Sort (삽입정렬)
정렬된 리스트 구형의 변형으로 볼 수 있다.
1. n번의 insert 연산은 O(n^2)을 가진다.
2. n번의 remove_min 연산은 O(n)을 가진다.

{% include pseudocode.html id="3" code="
\begin{algorithm}
\caption{Insertion Sort}
\begin{algorithmic}
\PROCEDURE{INSERTIONSORT}{$A$}
    \STATE $n$ $\leftarrow$ size$(A)$
    \FOR{$i = 1$ \TO $n$}       
        \STATE $x$ $\leftarrow A[i]$    \COMMENT{move forward entries $> x$}
        \STATE $j \leftarrow i$
        \WHILE{$j > 0$ \AND $x < A[j-1]$}
            \STATE $A[j] \leftarrow A[j-1]$
            \STATE $j \leftarrow j - 1$ 
        \ENDWHILE
        \STATE  \COMMENT{if $j > 0 \Rightarrow x \geq A[j-1]$}
        \STATE  \COMMENT{if $j < i \Rightarrow x < A[j+1]$}
    \ENDFOR
\ENDPROCEDURE
\end{algorithmic}
\end{algorithm}
" %}

이때, 우리는 top level loop의 invariant는 다음과 같다고 말할 수 있다:
- A(0, i]가 priority queue가 된다 - 따라서 정렬된다.
- A[i,n)는 삽입될 리스트이다.

예시:
![Insertion Sort]](/assets/img/insertion_sort.png)