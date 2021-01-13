---
layout: post
title:  "[백준 9935: 문자열 폭발]"
date:   2021-01-13 20:59:56 +0900
categories: jekyll update
---

Python Code

{% highlight ruby %}

s = input()
exp = input()

stack = []
j = 0
for i in range(len(s)):
    stack.append(s[i])
    if stack[-len(exp):] == list(exp):
        for j in range(len(exp)):
            stack.pop()

if len(stack) == 0:
    print("FRULA")
else:
    print(''.join(stack))

{% endhighlight %}

처음에는 쇼트코딩을 하려고

{% highlight ruby %}

while s.find(exp) != -1:
    s.replace(exp, "")

{% endhighlight %}

이런식으로 while문안에 find함수와 replace함수를 사용하였는데 시간초과가 났다.

그래서 리스트를 사용하여 stack을 구현해서 한 문자씩 append해주고 만약 exp만큼의 마지막 문자열이 exp와 동일하다면 exp의 길이만큼 pop을 해주었다.