---
layout: post
title:  "[백준 5639: 이진 검색 트리]"
date:   2020-10-30 16:42:56 +0900
categories: jekyll update
---

이진 탐색 트리(Binary Search Tree)는 다음 과 같은 특성을 갖는다.
1. 노드의 왼쪽 SubTree에 있는 모든 노드들의 키는 노드의 키보다 작다.
2. 노드의 오른쪽 SubTree에 있는 모든 노드들의 키는 노드의 키보다 크다.
3. 왼쪽, 오른쪽 SubTree도 이진 탐색 트리이다.

먼저 TreeNode를 LinkedList 형태로 연결시키기 위해 TreeNode class를 정의하였다.
그 후, insertKey 메서드를 통해 root노드에 자식노드를 계속 재귀적으로 추가해주고 root 노드를 return한다.

마지막으로 postorder(후위순휘) 방식으로 트리를 순회하여 Left -> Right -> Root 순서대로 키 값을 출력해준다.

# TreeNode class
{% highlight ruby %}
public class TreeNode {
	int data;
	TreeNode left;
	TreeNode right;
	
	public TreeNode() {
		this.left = null;
		this.right = null;
	}
	
	public TreeNode(int data) {
		this.data = data;
		this.left = null;
		this.right = null;
	}
	
	public Object getData() {
		return data;
	}
}
{% endhighlight %}

{% highlight ruby %}
import java.io.*;

public class BinarySearchTree {

	private static TreeNode root = null;
	
	public static TreeNode insertKey(TreeNode root, int x) {
		TreeNode p = root;
		TreeNode newNode = new TreeNode(x);
		
		if(p == null) {
			return newNode;
		}
		else if(p.data > newNode.data) {
			p.left = insertKey(p.left, x);
			return p;
		}
		else if (p.data < newNode.data) {
			p.right = insertKey(p.right, x);
			return p;
		}
		else
			return p;
	}
	
	public static void insertBST(int x) {
		root = insertKey(root, x);
	}
	
	public static void postorder(TreeNode root) {
		if(root != null) {
			postorder(root.left);
			postorder(root.right);
			System.out.println(root.data);
		}
	}
	
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		
		String input = "";
		
		while((input = br.readLine()) != null) {
			int data = Integer.parseInt(input);
			insertBST(data);
		}
		br.close();
		postorder(root);

	}

}

{% endhighlight %}