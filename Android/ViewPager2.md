# Android

## ViewPager2 를 활용해 VerticalCardSwiper 개발하기
아래의 화면처럼 화면에서 다음 item이 살짝 보이는 화면을 구현하려고 라이브러리를 찾아봤으나 아쉽게도 안드로이드에는 없었다. 그래서 해당 기능을 안드로이드로 개발해보고 이 글을 통해 개발 방법을 정리해봤다.

![example](https://user-images.githubusercontent.com/41943129/218264234-d638a645-f046-43df-a533-5aecc825e577.gif)

ViewPager2는 기존 ViewPager와 달리 Recyclerview.adpater를 상속해서 Adapter를 구현할 수 있다. ViewPager2 안에서 보여줄 item을 담기 위해서 Adapter를 구현한다.

VerticalViewPagerAdapter.kt
```kotlin
class VerticalViewPagerAdapter(var list : ArrayList<Int>) : RecyclerView.Adapter<VerticalViewPagerAdapter.PagerViewHolder>(){
    
    inner class PagerViewHolder(parent : ViewGroup): RecyclerView.ViewHolder
        (LayoutInflater.from(parent.context).inflate(R.layout.card_item_view, parent, false)) {
        ...
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): PagerViewHolder = PagerViewHolder(parent)

    override fun getItemCount(): Int = list.size

    override fun onBindViewHolder(holder: PagerViewHolder, position: Int) {
        ....
    }
}
```
ViewPager2 Adapter 설정 시 주의해야 할 점이 있는데, ViewPager2는 초기화 시 enforceChildFillListener를 등록한다. 해당 메서드는 `layoutParam`이 `width`와 `height`가 `match_parent`이 아닐 경우 예외를 발생시킨다. 그래서 item의 xml 생성 시 width와 height는 `match_parent`로 설정해야 한다  

Adapter 생성 후 세로 방향의 ViewPager를 구현 해야 하므로 `orientation` 메소드를 통해 ViewPager의 방향을 설정해야 한다.

```kotlin
verticalViewPager.orientation = ORIENTATION_VERTICAL
```
또한, 구현한 adapter를 연결한다
```kotlin
verticalViewPager.adapter = adapter
```

ViewPager2는 화면에 item 하나씩만 보여준다. 그래서 다음 item을 보여주기 위해 슬라이드 하는 경우 정해놓은 영역을 벗어나도록 해야한다. viewPager에서 제공하는 메소드인 `clipToPadding`, `clipChildren`, `offscreenPageLimit`를 활용한다.

`clipToPadding` 메소드는 ViewHolder에서 설정해 놓은 패딩된 영역으로 조정하는 메소드 이다. 기본값은 true이며, 현재는 item이 정해놓은 영역이 아닌 앞쪽으로 이동해서 현재 페이지에 보일 수 있게 해야하므로 `false`로 선언한다.
  
`clipChildren` 메소드는 item을 해당 범위 내에 보여주는 메소드이다. 마찬가지로 범위를 벗어나야 하기 때문에 `false`로 선언한다.

`offscreenPageLimit` 메소드는 페이지를 로드 했을 때 미리 로드할 페이지의 갯수를 선언하는 메소드다. 다음 item을 하나만 보여주면 되기 때문에 `1`로 선언한다. 만약 양쪽에 item을 보여주는 경우에는 2로 선언한다.

```kotlin
with(verticalViewPager){
    clipToPadding = false
    clipChildren = false
    offscreenPageLimit = 1
}
```

이동시 현재 item 위로 swipe 하려는 item이 겹치는 형태로 보여줘야 하므로 setPageTransformer 메소드를 통해 스크롤 이벤트를 구현한다

`setPageTransformer` 메소드는 현재 focus 된 page 뷰 객체의 정보를 받고, focus된 페이지로 부터 떨어져 있는 비율 정보를 position으로 받는다.  

처음 페이지를 로드 했을 경우 앞서 미리 로드할 페이지 갯수를 1로 선언했기 때문에 첫번째 페이지의 position 은 0f 값, 두번째 페이지는 1.0f 값을 갖는다.
이 정보를 활용해서 position을 분기 처리해 두번째 페이지의 Y좌표를 이동시킨다.

`setPageTransformer` 내부의 `transformPage` 메소드에 아래 내용을 구현한다.

먼저, item 간격의 margin과 offset을 dimen.xml 파일에 구현하고, 각 item의 높이를 구한다.
```kotlin
private val pageMarginPy = resources.getDimensionPixelOffset(R.dimen.pageMargin)
private val offsetPy = resources.getDimensionPixelOffset(R.dimen.offset)
private val pageHeight = screenHeight - pageMarginPy - offsetPy
```

그리고 ViewPager의 item은 드래그 할 때 position의 값이 대부분 -1.0f ~0.0f, 0.0f ~ 1.0f 범위내에서 이동하기 때문에 position를 분기처리한다.

```kotlin
if (position <= 0) { // [-1,0]
    translationY = pageHeight * -position

    scaleX = scaleFactor
    scaleY = scaleFactor
    alpha = (MIN_ALPHA + (((scaleFactor - MIN_SCALE) / (1 - MIN_SCALE)) * (1 - MIN_ALPHA)))
} else if (position <= 1) { // (0,1]
    alpha = 1f
    scaleX = 1f
    scaleY = scaleFactor

    val viewPager = page.parent.parent as ViewPager2
    val offset = position * -(2 * offsetPy + pageMarginPy)

    if (viewPager.orientation == ORIENTATION_VERTICAL) {
        page.translationY = offset
    } else {
        page.translationX = offset
    }

}
```
`position<=0` 인 경우에는 현재 페이지를 보여주고, 다음페이지가 현재 페이지 위치에 왔을 때 호출 된다. 

swipe 하는 경우 다음 item이 현재 item 위로 이동해야 하므로 `translationY` 함수를 활용해 page item의 위치를 빼서 다음 item이 현재 위치로 이동하도록 한다.

`position<=1` 인 경우는 다음페이지를 보여줘야 하기 때문에 `translationY` 메소드의 값을 position에서 offset과 margin의 값을 뺀다. 다음 페이지가 원래 보여져야 할 범위를 넘어서 보여주기 때문에 y좌표를 마이너스 만큼 이동시킨다.

또한 각각의 item들은 swipe 하면서 0.75 비율 만큼 줄어들기 때문에 `scaleX`와 `scaleY` 메소드에 최소 비율을 구하는 값을 선언한다.
```kotlin
scaleX = scaleFactor
scaleY = scaleFactor
```

swipe 시 position이 대부분 -1.0f ~ 1.0f 범위 내에서 호출되기 때문에 그 외의 범위 내에선 페이지를 보이지 않도록 해야한다. 

그러므로 그 외의 범위에서는 item의 크기를 줄여주거나 화면에서 item이 보이지 않도록 한다.
```kotlin
if (position < -1) { // [-Infinity,-1)
    scaleX = scaleFactor
    scaleY = scaleFactor
    alpha = 0f
}
```
position을 분기처리 한 후 구현한 내용을 setPageTransformer 에서 초기화 하면 아래와 같은 화면을 확인할 수 있다.

![movie](https://user-images.githubusercontent.com/41943129/218264259-4c2a74f1-460f-4f63-aa98-a2bdb814e9f4.gif)



### **전체 소스코드**
https://github.com/zjaym1703/VerticalCardView

### **Reference**
- ViewPager2 animation 
    - https://developer.android.com/training/animation/screen-slide-2?hl=ko
- ViewPager offscreenPageLimit 메모리 상태
    - http://coolsharp.blogspot.com/2018/11/viewpager-offscreenpagelimit.html
- ViewPager2 알아보기
    - https://velog.io/@eia51/ViewPager2-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0

