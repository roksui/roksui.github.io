---
#layout: post
title:  "[백준 1890: 점프]"
#date:   2020-11-01 22:59:56 +0900
#categories: jekyll update
---

완전탐색을 하여 DP를 사용하여 각 칸마다 도달할 수 있는 경우의 수를 저장해준다.

{% highlight ruby %}
import java.io.*;
import java.util.*;

public class DPJump {

	static int n;
	static int[][] map;
	static long[][] dp;
	
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = null;
		
		n = Integer.parseInt(br.readLine());
		map = new int[n][n];
		dp = new long[n][n];
		
		for(int i = 0; i < n; i++) {
			st = new StringTokenizer(br.readLine());
			for(int j = 0; j < n; j++) {
				map[i][j] = Integer.parseInt(st.nextToken());
			}
		}
		
		dp[0][0] = 1;
		
		for(int i = 0; i < n; i++) {
			for(int j = 0; j < n; j++) {
				
				if(i == n-1 && j == n-1) {
					continue;
				}
				if(i+map[i][j] < n)
					dp[i+map[i][j]][j] += dp[i][j];
				
				if(j + map[i][j] < n)
					dp[i][j+map[i][j]] += dp[i][j];
			}
		}
		System.out.println(dp[n-1][n-1]);
	}
}
{% endhighlight %}