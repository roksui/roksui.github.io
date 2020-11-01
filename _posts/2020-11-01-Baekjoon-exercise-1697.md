---
layout: post
title:  "[백준 1697: 숨바꼭질]"
date:   2020-11-01 22:30:56 +0900
categories: jekyll update
---

3번의 경우의 수의 움직임을 다 시험해보는 BFS를 돌려 수빈이가 동생의 포지션으로 가면 축적해놓은 시간을 리턴해준다.

{% highlight ruby %}
import java.util.*;
import java.io.*;

public class HideAndSeek {
	
	static int[] check = new int[100001];
	static int N,K;

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		if(N == K) System.out.print(0);
		else BFS(N);
	}
	
	
	public static void BFS(int n) {
		Queue<Integer> queue = new LinkedList<>();
		check[n] = 1;
		queue.offer(n);
		
		while(!queue.isEmpty()) {
			int x = queue.poll();
			
			for(int i = 0; i < 3; i++) {
				int next;
				
				if(i == 0 && x != 0) {
					next = x - 1;
				}
				else if(i == 1) {
					next = x + 1;
				}
				else {
					next = x * 2;
				}
				
				if(next == K) {
					System.out.println(check[x]);
					return;
				}
				
				if(next >= 0 && next < check.length && check[next] == 0) {
					queue.add(next);
					check[next] = check[x] + 1;
				}
			}		
		}
	}
}
{% endhighlight %}