# Scrollview 안 지도fragment scroll 구현
Scrollview 안에 지도fragment(네이버 지도, 구글지도)를 추가하는 경우 지도를 움직이게 되면 의도와 다르게 Scrollview가 움직인다.

원인은 Scrollview가 터치 이벤트를 우선순위로 갖고 있기 때문에 fragment 내부의 터치 이벤트가 무시되는 상황이 발생한다.

### 구현방법
1. fragment 안에 투명 view를 두어 터치 이벤트의 우선순위를 fragment로 유도
```xml
 <fragment
        android:id="@+id/map_fragment"
        android:name="com.naver.maps.map.MapFragment"
        android:layout_width="match_parent"
        android:layout_height="180dp">
        <View
            android:id="@+id/ivMapTransparent"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:src="@android:color/transparent"/>
</fragment>
```
2. 추가한 view에 touch listener를 재정의 하여 하위 요소가 터치이벤트를 가로채지 못하도록 ```requestDisallowInterceptTouchEvent()``` 메소드를 호출
```Kotlin
ivMapTransparent.setOnTouchListener { view, motionEvent ->
    val action = motionEvent.action
    when (action) {
        MotionEvent.ACTION_DOWN -> {
            scrollView.requestDisallowInterceptTouchEvent(true)
            false
        }
        MotionEvent.ACTION_UP -> {
            scrollView.requestDisallowInterceptTouchEvent(false)
            true
        }
        MotionEvent.ACTION_MOVE -> {
            scrollView.requestDisallowInterceptTouchEvent(true)
            false
        }
        else -> true
    }
}
```
해당 방법은 scrollview 안에 scroll을 지원하는 view를 구현하는 경우 

**Reference**
- https://developer.android.com/training/gestures/viewgroup?hl=ko
