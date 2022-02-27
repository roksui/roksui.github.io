---
title: "Java - Sets"
categories: 
  - Java Basics
---

## Set Interface
Java에서 Set 인터페이스는 Collection 하위의 인터페이스로써, 원소들의 중복을 허용하지 않고 순서가 없다.
이를 구현하는 대표적인 두 클래스가 HashSet과 TreeSet이다.

### HashSet
JDK 1.2부터 제공된 HashSet 클래스는 해시 알고리즘을 사용하여 검색 속도가 매우 빠르다.
이러한 HashSet 클래스는 내부적으로 HashMap 인스턴스를 이용하여 요소를 저장한다.

다음 예제를 통해 HashSet의 특징을 알 수 있다.
{% highlight java %}
HashSet<String> set = new HashSet<>();
set.add("김철수");
set.add("권나비");
set.add("박창균");
set.add("김철수");

// enhanced for문 사용
for (String e : set) {
    System.out.print(e + " ");
}
// 권나비, 김철수, 박창균 (순서 보존 X, 중복 허용 X)

// Iterator 사용
Iterator<String> itr = set.iterator();
while (itr.hasNext()) {
    System.out.print(itr.next() + " ");
}
{% endhighlight %}

**NOTE**: Collection 인터페이스에서는 Iterator 인터페이스를 구현한 클래스의 인스턴스를 반환하는 iterator() 메소드를 정의하여 각 요소에 접근하도록 하고 있다.

위 코드를 통해 HashSet이 Set 인터페이스의 특징을 잘 보여주고 있다.
요소가 add되는 것이 순차적으로 저장되지 않고 해시함수를 통해 정렬이 되기 때문에 출력 순서가 달라진다. 또, 이미 존재하는 요소를 추가하려고 하면, 해당 요소를 저장하지 않는다 (add메소드는 false를 리턴함).

이때, 해당 HashSet에 이미 존재하는 요소인지를 파악하기 위해 내부적으로 다음과 같은 과정은 거친다.
1. 해당 요소에서 hashCode() 메소드를 호출하여 반환된 해시값으로 검색할 범위를 정한다.
2. 해당 범위 내의 요소들을 equals() 메소드로 비교한다.

String과 같이 이미 만들어진 자료형은 상관없으나 새로운 클래스를 만들었다면 HashSet에서 add() 메소드를 사용하여 중복 없이 새로운 요소를 추가하기 위해서는 hashCode()와 equals() 메소드를 상황에 맞게 오버라이딩해야 한다.

다음과 같은 Member 클래스를 통해 알아보자.
{% highlight java %}
public class Member {
    private int memberId;
    private String memberName;

    public Member() {}
    public Member(int memberId, String memberName) {
        this.memberId = memberId;
        this.memberName = memberName;
    }

    public int getMemberId() {
        return memberId;
    }

    public void setMemberId(int memberId) {
        this.memberId = memberId;
    }

    public String getMemberName() {
        return memberName;
    
    }

    public void setMemberName(String memberName) {
        this.memberName = memberName;
    }

    public String toString() {
        return memberName + "님의 아이디는 " + memberId + "입니다.";
    }

    @Override
    public int hashCode() {
        return memberId;
    }

    @Override
    public boolean equals(Object obj) {
        if(obj instanceof Member) {
            Member member = (Member)obj;
            return this.memberId == member.memberId;
        }
        return false;
    }

}
{% endhighlight %}

위와 같이 hashCode()를 재정의하여 memberId로 구분하도록 하였다. 그리고 equals() 메소드에서 입력 객체의 memberId와 this의 아이디가 같으면 같은 객체로 취급하도록 하였다.

실제로 실험을 해보자.

{% highlight java %}
public class MemberHashSet {
    private HashSet<Member> hs;

    public MemberHashSet() {
        hs = new HashSet<Member>();
    }

    public void addMember(Member member) {
        hs.add(member);
    }

    public boolean remove Member(int memberId) {
        Iterator<Member> itr = hs.iterator();
        while (itr.hasNext()) {
            Member member = itr.next();
            if (member.getMemberId() == memberId) {
                hs.remove(member);
                return true;
            }

            System.out.println("번호가 존재하지 않습니다.");
            return false;
        }
    }

    public void showAllMembers() {
        for (Member member : hs) {
            System.out.println(member);
        }
    }
}
{% endhighlight %}

{% highlight java %}
public class MemberHashSetTest {
    public static void main(String[] args) {

        MemberHashSet manager = new MemberHashSet();

        Member m1 = new Member(1, "김철수");
        Member m2 = new Member(2, "김영희");
        Member m3 = new Member(2, "김수아");

        manager.add(m1);
        manager.add(m2);
        manager.add(m3);

        manager.showAllMembers();
        // 김철수
        // 김영희
    }
}
{% endhighlight %}

m3는 m2와 memberId가 동일하므로 중복으로 처리되어 저장되지 않는다.

### TreeSet
TreeSet 클래스는 데이터가 정렬된 상태로 저장되는 이진 검색 트리(binary search tree) 형태로 요소를 저장한다.
이진 검색 트리는 데이터 insertion과 removal 연산 시간이 매우 빠르다.

다음의 예를 보자.

{% highlight java %}
TreeSet<Integer> ts = new TreeSet<Integer>();

ts.add(40);
ts.add(20);
ts.add(30);
ts.add(10);

for (int e : ts) {
    System.out.print(e + " "); // 10 20 30 40
}

ts.remove(40);

Iterator<Integer> iter = ts.iterator();
while (iter.hasNext()) {
    System.out.print(iter.next() + " "); // 10 20 30
}

System.out.println(ts.size()); // 3

System.out.println(ts.subSet(10,20)); // [10]
System.out.println(ts.subSet(10, true, 20, true)); // [10, 20]
{% endhighlight %}

출력 내용을 보면 알 수 있듯이, TreeSet 인스턴스에 저장되는 요소들은 모두 정렬된 상태로 저장된다.

또, 위에 예제에서 사용된 subSet() 메소드는 TreeSet 인스턴스가 정렬된 상태이기에 동작이 가능한, 해당 트리의 부분 집합만을 보여주는 메소드이다.
원형은 다음과 같다.
{% highlight java %}
public NavigableSet<E> subSet(E fromElement, E toElement)
public NavigableSet<E> subSet(E fromElement, boolean fromInclusive, E toElement, boolean toInclusive)
{% endhighlight %}

또, 내부적으로 이진 검색 트리에 데이터가 저장 될 때, 비교하여 저장될 위치를 정하게 된다. 즉, 객체 비교를 위해 Comparable이나 Comparator 인터페이스를 구현 해야 할 수도 있다.

예를 들어 String 객체들을 TreeSet에 저장해보자.

{% highlight java %}
public class TreeSetTest {
    public static void main(String[] args) {
        TreeSet<String> ts = new TreeSet<>();

        ts.add("김갑수");
        ts.add("이기상");
        ts.add("마동석");

        for (String str : ts) {
            System.out.print(str + " "); // 김갑수 마동석 이기상 순서로 출력
        }
    }
}
{% endhighlight %}

위와 같이 String 클래스에는 이미 Comparable 인터페이스를 구현되어 있어서 이름이 오름차순으로 정렬되어 저장되고, 그렇게 출력되는 것을 볼 수 있다.

참조자료형일 때, 직접 구현해야 할 필요가 있다.

{% highlight java %}
public class Member implements Comparable<Member> {
    private int memberId;
    private String memberName;

    public Member() {}
    public Member(int memberId, String memberName) {
        this.memberId = memberId;
        this.memberName = memberName;
    }

    public int getMemberId() {
        return memberId;
    }

    public void setMemberId(int memberId) {
        this.memberId = memberId;
    }

    public String getMemberName() {
        return memberName;
    
    }

    public void setMemberName(String memberName) {
        this.memberName = memberName;
    }

    public String toString() {
        return memberName + "님의 아이디는 " + memberId + "입니다.";
    }

    @Override
    public int hashCode() {
        return memberId;
    }

    @Override
    public boolean equals(Object obj) {
        if(obj instanceof Member) {
            Member member = (Member)obj;
            return this.memberId == member.memberId;
        }
        return false;
    }

    @Override
    public int compareTo(Member member) {
        return this.memberId - member.memberId;
    }
    
    // memberName으로 비교하고 싶을 경우, 다음과 같이 할 수 있음
    @Override
    public int compareTo(Member member) {
        return this.memberName.compareTo(member.getMemberName());
    }
}
{% endhighlight %}

{% highlight java %}
public class TreeSetTest {
    public static void main(String[] args) {
        TreeSet<Member> ts = new TreeSet<>();

        Member m1 = new Member(100, "김갑수");
        Member m2 = new Member(200, "이기상");
        Member m3 = new Member(300, "마동석");

        ts.add(m1);
        ts.add(m2);
        ts.add(m3);

        for (Member member : ts) {
            System.out.print(memberName + " "); // 김갑수 이기상 마동석 순서로 출력 (memberId 오름차순)
        }
    }
}
{% endhighlight %}

출처
- https://velog.io/@foeverna
- http://tcpschool.com/java/java_collectionFramework_set
