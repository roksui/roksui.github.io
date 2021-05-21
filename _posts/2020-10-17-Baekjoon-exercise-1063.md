---
#layout: post
title:  "[백준 1063: 킹]"
#date:   2020-10-17 22:10:56 +0900
#categories: jekyll update
---

아직 익숙하지 않은 시뮬레이션 알고리즘이었다.

풀이의 핵심은 move라는 함수를 정의해서 초기 킹의 위치, 돌의 위치, 그리고 주어진 움직임 배열을 인자로 받아서, 해당 배열의 움직임에 따라 move라는 정수를 선언하여 위치에 더해주는 것이었다. 

또, 주의했던 점은 킹과 돌이 판 밖으로 나가면 위치 업데이트를 해주지 않고 킹과 돌이 서로 collide하면 돌도 같이 움직여주는 예외처리를 하는 것이었다.

{% highlight ruby %}
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class King {

	static int king_pos;
	static int rock_pos;
	static int N;
	static String[] movements;
	
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = null;
		
		st = new StringTokenizer(br.readLine());
		
		king_pos = positionConverter(st.nextToken());
		rock_pos = positionConverter(st.nextToken());
		N = Integer.parseInt(st.nextToken());
		movements = new String[N];
		
		for(int i = 0; i < N; i++) {
			movements[i] = br.readLine();
		}
		
		
		move(king_pos, rock_pos, movements);
	}
	
	public static void move(int king_pos, int rock_pos, String[] movements) {
		
		int king_nextpos = king_pos;
		int rock_nextpos = rock_pos;
		int move = 0;
		Map<Integer, String> map = new HashMap<>();
		
		map.put(1, "A");
		map.put(2, "B");
		map.put(3, "C");
		map.put(4, "D");
		map.put(5, "E");
		map.put(6, "F");
		map.put(7, "G");
		map.put(8, "H");
		
		for(int i = 0; i < movements.length; i++) {	
			switch(movements[i]) {
			case "R": 
				move = 1;
				break;
			case "L": 
				move = -1;
				break;
			case "B":
				move = -10;
				break;
			case "T":
				move = 10;
				break;
			case "RT":
				move = 11;
				break;
			case "LT":
				move = 9;
				break;
			case "RB":
				move = -9;
				break;
			case "LB":
				move = -11;
				break;
			}
			
			king_nextpos = king_nextpos + move;
			
			if(king_nextpos == rock_nextpos) {
				rock_nextpos = rock_nextpos + move;
			}
			
			
			if(king_nextpos % 10 == 9 || king_nextpos % 10 == 0 || king_nextpos / 10 == 9 || king_nextpos / 10 == 0) {
				king_nextpos = king_nextpos - move;
			}
			if(rock_nextpos % 10 == 9 || rock_nextpos % 10 == 0 || rock_nextpos / 10 == 9 || rock_nextpos / 10 == 0) {
				rock_nextpos = rock_nextpos - move;
				king_nextpos = king_nextpos - move;
			}
		}
		
		System.out.println(map.get(king_nextpos % 10) + king_nextpos / 10);
		System.out.println(map.get(rock_nextpos % 10) + rock_nextpos / 10);
	}
	
	public static int positionConverter(String pos) {
		int num = 0;
		int result = 0;
		switch(pos.charAt(0)) {
		case 'A':
			num = 1;
			break;
		case 'B':
			num = 2;
			break;
		case 'C':
			num = 3;
			break;
		case 'D':
			num = 4;
			break;
		case 'E':
			num = 5;
			break;
		case 'F':
			num = 6;
			break;
		case 'G':
			num = 7;
			break;
		case 'H':
			num = 8;
			break;
		}
		
		result = num + 10 * Integer.parseInt(pos.charAt(1) + "");
		
		return result;
	}
}
{% endhighlight %}