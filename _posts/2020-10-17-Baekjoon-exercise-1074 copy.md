---
layout: post
title:  "[백준 1074: Z]"
date:   2020-10-17 22:03:56 +0900
categories: jekyll update
---

재귀함수를 써서 풀면 되는 문제였다.
처음에는 2차원 배열을 만들어서 시도하려고 하였으나 N이 15까지 가기 때문에 메모리초과가 나기때문에 적절한 방법은 아니다.

네모의 크기와 좌표를 인자로 받는 search함수를 재귀적으로 정의한다.

먼저 원래의 크기 2^N부터 시작하여 계속해서 절반으로 쪼개고, 쪼갠 후 4개의 quadrant를 재귀적으로 모두 확인하여 좌표를 방문할때 마다, 즉 size가 1이 될때마다 count를 올려준다.

만약 (r,c)와 일치하는 (x,y)좌표가 있다면 count를 출력해준다.

NOTE: 이 재귀 솔루션은 런타임이 매우 오래 걸린다는 단점이 있다. 아마, 2차원 배열의 각 좌표 값의 패턴을 찾아서 푸는 방법도 있을 것이다. 추후에 고민해봐야 겠다.

{% highlight ruby %}
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Z {

	static int N, c, r;
	static int count;
	
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		r = Integer.parseInt(st.nextToken());
		c = Integer.parseInt(st.nextToken());
		
		int pow = (int)Math.pow(2, N);
		
		search(0,0,pow);	
	}
	
	public static void search(int x, int y, int size) {
		if(size == 1) {
			//System.out.println(x +" " + y + " " + size);
			if(r == x && c == y) {
				System.out.println(count);
			}
			
			count++;
			return;
		}
		
		int half = size / 2;
		
		//System.out.println(x +" " + y + " " + size);
		
		search(x, y, half);
		search(x, y+half, half);
		search(x+half, y, half);
		search(x+half, y+half, half);
	}

}
{% endhighlight %}