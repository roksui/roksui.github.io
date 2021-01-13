---
layout: post
title:  "[백준 1316: 그룹 단어 체커]"
date:   2021-01-09 22:59:56 +0900
categories: jekyll update
---

Python Code

{% highlight ruby %}

n = int(input())
cnt = 0
words = []

for i in range(n):
    appeared = set()
    word = input()
    for character in word:
        if character not in appeared:
            appeared.add(character)
        elif prev_char == character:
            continue
        else:
            break
        prev_char = character
    else:
        cnt += 1
        continue
    continue
print(cnt)

{% endhighlight %}

입력 받은 스트링을 하나씩 검사하고 inner for loop으로 문자 하나하나 확인한다. Set을 하나 만들어서 만약 같은 문자가 이미 존재하면 break를 해준다. 단, 만약 이전 문자와 현재 문자가 같은 경우는 continue해준다.

인터넷 코드를 찾아보니 훨씬 더 간결한 코드를 찾을 수 있었다.

{% highlight ruby %}
result = 0
for i in range(int(input())):
    word = input()
    if list(word) == sorted(word, key=word.find):
        result += 1
print(result)
{% endhighlight %}

list(word)를 하면 입력받은 스트링이 'abca'일 경우 ['a','b','c','a'] 가 되고 sorted()는 스트링에서 찾는 문자를 기준으로 정렬한다. 따라서 ['a','a','b','c']가 되고 다르기 때문에 result를 증가시키지 않는다. 만약 그룹단어라면 결과가 같게된다.