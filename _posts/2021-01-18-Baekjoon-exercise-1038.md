---
#layout: post
title:  "[백준 1038: 감소하는 수]"
#date:   2021-01-18 20:31:56 +0900
#categories: jekyll update
---

Python Code

{% highlight ruby %}

N = int(input())

if N > 1022:
    print(-1)
elif N <= 10:
    print(N)

else:
    queue = []
    cnt = 0
    for i in range(1, 10):
        queue.append(i)
        cnt += 1

    while cnt < N:
        k = queue.pop(0)
        temp = k % 10
        for i in range(temp):
            queue.append(k*10 + i)
            cnt += 1

            if cnt == N:
                print(k*10 + i)
                break
{% endhighlight %}

input의 개수가 1023개 이상이 되면 -1을 리턴하는 것이 핵심인 것 같다. 왜냐하면 [1, 2, 3, 4, 5, 6, 7, 8, 9]라는 리스트가 있을 때, 여기서 임의로 n개의 수를 골랐을 때 만들 수 있는 감소하는 수는 항상 unique하다. 예를 들어 4, 7, 2을 고르면 742 한 가지의 감소하는 수를 만들 수 있다. 따라서 감소하는 수의 개수는 이 리스트의 부분집합 (공집합을 제외한)의 개수가 된다. 따라서 2^10 - 1 = 1023개다.

이제 그 이하의 N에 대한 감소하는 수를 찾기 위해 Queue 자료구조를 사용하였다. 처음에 1~10을 차례대로 넣어주고 하나씩 pop하여 % 10 연산을 통해 1의자리 수를 구해서 그보다 큰 감소하는 수를 for문을 통해 다시 Queue에 append해준다.