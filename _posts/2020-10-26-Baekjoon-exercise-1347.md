---
layout: post
title:  "[백준 1347: 미로 만들기]"
date:   2020-10-26 18:32:56 +0900
categories: jekyll update
---

움직임에 따라 방향 조정을 해주는 rotate함수를 만들어 방향을 바꿔준다.

앞으로 움직일 경우에는 방향에 따라 현재 포지션에서 좌표값을 더해준다.

움직인 것에 따라서 리스트에 좌표값을 넣어주고 그 행동 범위에 맞는 사이즈의 배열을 만든 후, (0,0)좌표 값을 왼쪽 가장 위쪽으로 바꿔준다.

리스트에 있는 좌표값은 '.'로 찍어주고 나머지는 '#'으로 찍어준다.

{% highlight ruby %}
import java.io.*;
import java.util.ArrayList;

public class MakeMaze {
	
	static xy cur_pos;
	static int cur_dir;
	
	static void rotate(char dir) {
		if(dir == 'R'){
			++cur_dir;
			if(cur_dir > 3)
				cur_dir = 0;
		}
		else if(dir == 'L'){
			--cur_dir;
		    if(cur_dir < 0)
		    	cur_dir = 3;
		}
	}
	
	public static void main(String[] args) throws NumberFormatException, IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		
		int N = Integer.parseInt(br.readLine());
		
		String moves = br.readLine();
		ArrayList<xy> arr = new ArrayList<>();
		
		cur_dir = 2;
		cur_pos = new xy(0, 0);
		arr.add(new xy(0,0));
		
		int max_x = 0, max_y = 0;
		int min_x = 0, min_y = 0; // Integer.MIN_VALUE / MAX_VALUE 로 하면 런타임 에러가 뜸
		int maze_size_x = 0, maze_size_y = 0;
		
		for(int i = 0; i < N; i++) {
			if(moves.charAt(i) == 'F') {
				if(cur_dir == 0) {
					cur_pos.x += -1;
				}
				else if(cur_dir == 1) {
					cur_pos.y += 1;
				}
				else if(cur_dir == 2) {
					cur_pos.x += 1;
				}
				else if(cur_dir == 3) {
					cur_pos.y += -1;
				}
				arr.add(new xy(cur_pos.x, cur_pos.y));
				
				//System.out.println(cur_pos.x + " " + cur_pos.y);
				max_x = Math.max(max_x, cur_pos.x);
				max_y = Math.max(max_y, cur_pos.y);
				//System.out.println(max_x + " " + max_y);
				min_x = Math.min(min_x, cur_pos.x);
				min_y = Math.min(min_y, cur_pos.y);
			}
			else {
				rotate(moves.charAt(i));
			}
			
		}
		
		maze_size_x = Math.abs(max_x-min_x);
		maze_size_y = Math.abs(max_y-min_y);
		int[][] maze = new int[maze_size_x + 1][maze_size_y + 1];
		
		//System.out.println(min_x + " " + min_y);
		for(xy pos : arr) {
			//System.out.println(pos.x + " " + pos.y);
			int x = pos.x + -1*min_x;
			int y = pos.y + -1*min_y;
			maze[x][y] = 1;
		}
		
			
		for(int i = 0; i < maze.length; i++) {
			for(int j = 0; j < maze[i].length; j++) {
				if (maze[i][j] == 1)
					System.out.print('.');
				else
					System.out.print('#');
			} 
			System.out.println();
		}

	}
	
}
{% endhighlight %}