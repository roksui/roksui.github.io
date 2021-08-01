---
title: Android - RecyclerView
---

## What is it?
Recycle이라는 단어에서 알 수 있듯이 같은 것을 다시 이용(재활용)한다는 의미를 가진다.

예를 들어, 인스타그램 피드와 같이 동일한 형태의 뷰에 데이터에 따라서 달라지는 형태의 뷰를 리사이클러 뷰라고 한다.

리사이클러 뷰에서는 다음과 같은 사항을 고려해야 한다.
1. 어떠한 형태로 아이템들이 들어갈 것인지
2. 어떠한 데이터가 들어갈 것인지
3. 어디에 어떤 데이터가 들어갈 것인지
4. 리사이클러 뷰에 어떻게 데이터들을 연결시킬 것인지

### 1. 어떠한 형태로 아이템들이 들어갈 것인지
반복될 개체를 아이템이라고 부른다.\
item_somename.xml 형태로 레이아웃 파일을 만들고, 기존에 layoutㅍ 파일을 만드는 방식과 동일하게 만들면 된다.

아이템 레이아웃이 결정되었다면 리사이클러 뷰에서 이 아이템들이 어떻게 보여질지를 리사이클러 뷰 태그 내에서 LayoutManager 종류를 정해준다. 예를 들어 다음과 같이 작성할 수 있다.

```
<androidx.recyclerview.widget.RecyclerView
            app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
            android:orientation="vertical"
            tools:listitem="@layout/item_search_main_goodssearch"
            ... />
```

위와 같이 LinearLayoutManager vertical로 설정하면 리스트 형태로 뷰가 나온다. GridLayoutManager로도 선택할 수 있다.

tools:listitem을 적용하면 리사이클러뷰 내에서 위의 Layout 형태로 뷰를 미리볼 수 있다.

### 2. 어떠한 데이터가 들어갈 것인지?
레이아웃에 들어갈 데이터의 형태를 코틀린에서는 Data Class로 한다.
Data Class는 아이템에 들어갈 데이터에 대한 기능을 제공한다.
- equals
- hashCode
- toString
- copy
위와 같은 메소드를 사용할 수 있게 해준다.

예를 들어 아이템에 사진, 이름, 부가 설명의 데이터가 들어간다면 다음과 같이 데이터 클래스를 정의할 수 있다.

```
data class LayoutData (
    val img : String,
    val name : String,
    val content : String
)
```

### 3. 어디에 어떤 데이터가 들어갈 것인지?
데이터가 결정되었으니 이제 이를 실제 레이아웃과 연결을 시켜줘야 한다.
만약 전형적인 레이아웃에서 뷰를 찾아주는 findViewById를 사용한다면 많은 수고로움이 생길 것이다. 그러나 리사이클러 뷰에서는 ViewHolder라는 방법을 사용하여 효율성을 높일 수 있다.

ViewHolder는 클래스 내에 View를 저장하는 변수를 만들어 그안에 데이터를 직접 연겨시킬 수 있는 클래스, 디자인 패턴이다. 

쉽게 말해, 데이터 클래스 형식으로 저장된 변수를 실제 레이아웃의 데이터로 연결시키는 역할을 해준다.

그럼 ViewHolder에는 아래 두 가지 핵심 내용이 필요할 것이다.
1. View를 저장할 수 있는 변수
2. View와 데이터를 연결시키는 함수

```
class MyViewHolder(private val binding: View ViewDataBinding) : RecyclerView.ViewHolder(binding, root) {
    fun bind(layoutData : LayoutData) {
        binding.img = layoutData.img
        binding.name = layoutData.name
        ...
    }
}
```

### 4. 리사이클러 뷰에 어떻게 데이터들을 연결시킬 것인지?
뷰홀더를 통해 데이터가 틀 안에 들어갈 수 있는 메소드를 정의하였다.

이제 이 메소드를 실제로 사용할 수 있는 구현체를 만들어야 한다. 데이터를 받아와서 이를 레이아웃에 직접 연결하는 함수를 실행시키는 클래스를 만들어 줄 것인데, 이러한 친구를 Adapter라고 부른다.

Adapter는 RecyclerView.Adapter 제너릭 클래스를 상속받아서 사용한다.

```
class MyAdapter : RecyclerView.Adapter<MyAdapter.MyViewHolder>() {
    private var data = mutableListOf<LayoutData>()

    class MyViewHolder(private val binding: View ViewDataBinding) : RecyclerView.ViewHolder(binding, root) {
        fun bind(layoutData : LayoutData) {
            binding.img = layoutData.img
            binding.name = layoutData.name
            ...
        }
    }

    override fun onCreateViewHolder(parent : ViewGroup, viewType : Int) : FeatureViewHolder {
        val layoutInflater = LayoutInflater.from(parent.context)
        val binding = DataBindingUtil.inflate(layoutInflater, R.layout.item_somename, parent, false)
        return MyViewHolder(binding)
    }

    override fun getItemCount() : Int = data.size

    override fun onBindViewHolder(holder : MyViewHolder, position : Int) {
        holder.onBind(data[position])
    }

    fun replaceList(newList : MutableList<LayoutData>) {
        data = newList.toMutableList()
        notifyDataSetChanged()
    }
}
```

### 5. Activity/Fragment에서 연결하기
어댑터 인스턴스를 만들고, 데이터를 어댑터 안에다 넣고, 어댑터 안에 데이터가 바뀌었다는 notify를 하고 실제 리사이클러 뷰의 어댑터를 우리가 만든 어댑터로 설정해주는 과정이 필요하다.

```
val mDatas = mutableListOf<LayoutData>()

myAdapter = MyAdapter()

mDatas.apply(
    add(
        LayoutData(
            ...
        )
    )
    add(
        LayoutData(
            ...
        )
    )
    ...
)

myAdapter.replaceList(mDatas)

binding.rvMyRecyclerView.adapter = myAdapter
```