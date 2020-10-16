---
layout: post
title:  "[백준 10250: ACM 호텔]"
date:   2020-10-16 15:03:56 +0900
categories: jekyll update
---

mod 연산자와 / 연산자를 사용한 수식을 통해 해당 n번째 손님의 호수를 찾는 방식을 사용하였다.

호텔 방은 위에서 아래로 왼쪽에서 오른쪽 부터 채워지기 때문에 예를 들어 높이가 6고 길이가 8인 호텔에 5번째 손님은 501호에 배정된다.
6번째 손님은 601호에 배정되고, 7번째 손님은 102호로 배정된다.

여기서 발견한 패턴이, 배정되는 호수의 hundreds 자리에는 n % h가 되고, 그 뒤에 자리 숫자는 n / h + 1이 된다.

단, 만약 n == h일 경우, n % h 가 0이되므로 hundreds 자리는 h값이 되고 뒤에 자리 숫자는 n / h가 된다. 

{% highlight ruby %}
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class ACMHotel {
	
	public static void main(String[] args) throws NumberFormatException, IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st;
		
		int T = Integer.parseInt(br.readLine());
		int[] H = new int[T];
		int[] W = new int[T];
		int[] n = new int[T];
		int[] result = new int[T];
		
		for(int i = 0; i < T; i++) {
			st = new StringTokenizer(br.readLine());
			H[i] = Integer.parseInt(st.nextToken());
			W[i] = Integer.parseInt(st.nextToken());
			n[i] = Integer.parseInt(st.nextToken());
			
			if(n[i] % H[i] == 0)
				result[i] = 100 * (H[i]) + (n[i] / H[i]);
			else
				result[i] = 100 * (n[i] % H[i]) + (1 + n[i] / H[i]);
		}
		
		for(int i = 0; i < T; i++)
			System.out.println(result[i]);
	}

}
{% endhighlight %}