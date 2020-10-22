---
layout: post
title:  "[백준 1051: 숫자 정사각형]"
date:   2020-10-22 10:02:56 +0900
categories: jekyll update
---

# First Attempt

boolean값을 리턴하는 isPresent(int size) 함수를 정의하여, 정사각형의 변의 길이를 인자로 받아서 만약 꼭지점값이 동일한 정사각형이 직사각형 내에 존재하면 true를 리턴하게 하였다.

그리고 main 함수에서는 정사각형의 변의 길이를 max_size부터 시작하여 2가될때 까지 decrement해주면서 isPresent 여부를 확인한다.
여기서 max_size는 정사각형이 가질 수 있는 최대 변의 길이인데, 예를 들어 (N,M) = (3,5) 이면 max_size는 3이된다. 

{% highlight ruby %}
import java.io.*;
import java.util.*;

public class NumberSquare {

	static int N, M;
	static int[][] rect;
	static int max_size;
	
	
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		rect = new int[N][M];
		for(int i = 0; i < N; i++) {
			String str = br.readLine();
			for(int j = 0; j < M; j++) {
				rect[i][j] = (int)str.charAt(j) - 48;
			}
		}
		
		max_size = (N > M) ? M : N;
		
		for(int i = max_size; i > 1; i--) {
			if (isPresent(i)) {
				System.out.print(i * i);
				return;
			}
		}
		System.out.print(1);	
	}
	
	public static boolean isPresent(int size) {	
		if(size == max_size) {
			int i = 0, j = 0;
			while(true) {
				if(rect[i][j] == rect[i][size - 1 + j]){
					i += size - 1;
                    if(rect[i][j] == rect[i][size - 1 + j] && rect[i - size + 1][j] == rect[i][j]) return true;
					continue;
				}
				else {
					if(N < M) {
						j++;
						i = 0;
						if(j > M - size) break;
						continue;
					}
					else {
						i++;
						j = 0;
						if(i > N - size) break;
						continue;
					}
				}	
			}
		}
		
		else {
			int i = 0, j = 0;
			while(true) {
				if(j > M - size) {
					i++;
					j = 0;
				}
				if(rect[i][j] == rect[i][size - 1 + j]){
					i += size - 1;
					
					if(i > N) break;
					if(rect[i][j] == rect[i][size - 1 + j] && rect[i - size + 1][j] == rect[i][j]) return true;
					else {
						i -= size - 1;
						j++;
					}
					continue;
				}
				else {
					j++;
					
					if(i > N - size) break;
					continue;
				}
			}
		}
		return false;	
	}
}
{% endhighlight %}

적다 보니 코드가 매우 더러워졌다.. 그리고 런타임에러가 난다. max_size를 가지는 꼭지점이 동일한 정사각형이 있으면 최적의 런타임이 겠지만 만약 size가 1인 꼭지점이 동일한 정사각형만 존재한다면 최악의 경우가 발생한다.

또 한가지 실수를 한것이 위 꼭지점 쌍이 서로 같고 아래 꼭지점 쌍이 서로 같으면 되는 걸로 설정했다. 실은 네개의 값이 모두 동일해야 한다.

3중 for문으로 좀 더 직관적으로 코딩하였다.

# Second Attempt

{% highlight ruby %}

import java.io.*;
import java.util.*;

public class NumberSquare {

	static int N, M;
	static int[][] rect;
	
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		rect = new int[N][M];
		for(int i = 0; i < N; i++) {
			String str = br.readLine();
			for(int j = 0; j < M; j++) {
				rect[i][j] = (int)str.charAt(j) - 48;
			}
		}
		
		int ans = findMaxSize();
		System.out.print(ans);
	}
	
	public static int findMaxSize() {
		int size = 1;
		int sideLength = Math.min(N, M);
		
		for(int k = sideLength; k > 1; k--) {
			int temp_size = 1;
			for(int i = 0; i < N - k + 1; i++) {
				for(int j = 0; j < M - k + 1; j++) {
					if(rect[i][j] == rect[i][j + k - 1] && 
							rect[i + k - 1][j] == rect[i + k - 1][j + k -1] && rect[i][j] == rect[i + k -1][j]) {
						temp_size = k * k;
					}
				}
			}
			size = Math.max(size, temp_size);
		}
	
		return size;
	}

}

{% endhighlight %}