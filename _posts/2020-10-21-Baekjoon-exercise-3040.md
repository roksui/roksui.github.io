---
#layout: post
title:  "[백준 3040: 백설 공주와 일곱 난쟁이]"
#date:   2020-10-21 17:14:56 +0900
#categories: jekyll update
---

Brute Force를 사용하여 모든 경우의 수를 확인해야 하는 문제이다.

먼저 sum에 9명의 난쟁이의 키를 모두 더해 준다.

그리고 i, j를 이중 for문을 돌면서 두 명의 키를 제외한 값이 100이 되면 break를 해주면 끝이난다.

단, i, j 는 달라야 하는 조건을 추가해주어야 한다. -> (0,1), (0,2), (0,3) ... (3,4) ... etc

조금 더 성능을 높일 수 있는 방법은 print를 해주는 for문을 밖으로 빼내서 flag변수를 활용하여 두 개의 난쟁이를 제거하는 방법도 있을 것이다. 

{% highlight ruby %}
import java.io.*;

public class SevenDwarfs {

	public static void main(String[] args) throws NumberFormatException, IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		
		int sum = 0;
		int[] arr = new int[9];
		
		for(int i = 0; i < arr.length; i++) {
			arr[i] = Integer.parseInt(br.readLine());
			sum += arr[i];
		}
		
		out:for(int i = 0; i < arr.length; i++) {
			for(int j = 0; j < arr.length; j++) {
				if(i!=j && sum - arr[i] - arr[j] == 100) {
					for(int k = 0; k < arr.length; k++) {
						if(k!= i && k!= j)
							System.out.println(arr[k]);
					}
					break out;
				}		
			}
		}
	}
}
{% endhighlight %}