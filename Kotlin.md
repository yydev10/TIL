## 얕은복사 vs 깊은복사
### 얕은복사
- 복사하는 대상의 주소를 참조
- 같은 메모리를 참조하기 때문에 원본객체나 복사한 객체 중 하나의 값이 변경될 때 나머지 객체도 변경됨
```Kotlin
var a = User("name",22);
var b = a;
println(a); //User@907fd1
println(b); //User@907fd1
``` 
### 깊은복사
- 객체와 인스턴스가 모두 복사
- 복사하려는 객체의 주소에는 원본객체의 대이터가 저장됨
1. Cloneable 인터페이스 사용
- clone 함수 재정의 해서 객체를 복사
- 기본타입이 아닌 변수는 그 타입의 변수를 새로 복사해야 한다
```Kotlin
class User(...) : Cloneable{
    var id : Int,
    var score : MutableList<Int>
    ....
    public override fun clone() : User{
        var user = super.clone() as User //기본타입의 변수만 있을 땐 여기까지만 작성해도 됨
        user.score = mutableListOf<Int>().apply{ addAll(score) }
        return user
    }
}
```
2. copy() 사용
- data class의 copy는 주생성자의 프로퍼티만 복사하기 때문에 copy 함수를 직접 구현해야 한다
- Cloneable 인터페이스 처럼 기본타입이 아닌 변수는 copy 함수 내에 새로 복사해야 한다
```Kotlin
data class User(var id: Int, var list: MutableList<Int>) {
    fun copy() : User {
        return User(id, list.toMutableList())
    }
}
```
#### Reference
- https://kotlinlang.org/docs/data-classes.html#copying