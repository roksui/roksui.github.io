---
#layout: post
title:  "[백준 5543: 상근날드]"
#date:   2020-10-23 17:02:56 +0900
#categories: jekyll update
---

'상근날드' : 버거 세개 중에 가장 싼 것, 음료 두개 중에 가장 싼 것을 찾아 더하고 50을 빼주면 되는 간단한 수학 문제이다.

Math.min과 사용자 정의 함수 min의 성능을 비교해봤는데 결과는 똑같이 나왔다.


{% highlight ruby %}
import java.io.*;

public class Sangeunald {

	public static void main(String[] args) throws NumberFormatException, IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		
		int[] burgers = new int[3];
		int[] drinks = new int[2];
		
		burgers[0] = Integer.parseInt(br.readLine());
		burgers[1] = Integer.parseInt(br.readLine());
		burgers[2] = Integer.parseInt(br.readLine());
		drinks[0] = Integer.parseInt(br.readLine());
		drinks[1] = Integer.parseInt(br.readLine());
		
		//int cost = Math.min(drinks[0], drinks[1]) + Math.min(burgers[0], Math.min(burgers[1], burgers[2])) - 50;
		int cost = min(drinks[0], drinks[1]) + min(burgers[0], min(burgers[1], burgers[2])) - 50;
		System.out.print(cost);
		

	}
	
	public static int min(int a, int b) {
		int c;
		return c = (a < b) ? a : b;
	}

}
{% endhighlight %}