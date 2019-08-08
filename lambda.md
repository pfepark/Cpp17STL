# lambda expression

람다 표현식 또는 람다 함수는 클로저를 구성한다. 클러저는 함수처럼 호출될 수 있는 이름이 없는 객체를 일반적으로 일컫는 용어다. C++에서는 이러한 객체가 파라미터의 여부에 상관없이 반드시 ()  함수 호출 연산자를 구현해야한다.

람다표현식은 코드를 일반화해 깔끔하게 작성하는 데 큰 도움이 된다. 사용자가 정의한 특정 타입을 처리할 때 파라미터로 사용해 아주 일반화된 알고리즘을 특수화시킬 수 있다.

##### 람다 표현식을 이용해 실행 중인 함수 정의

```c++
auto count (
    [count = 0]() mutable { return ++count; }
);
// 대괄호 안에 count = 0을 작성해 넣어 정수 0으로 초기화된 내부 카운터 변수 count가 있다는 점을 알려준다.
```

[ 캡쳐 목록] ( 파라미터 )

​	mutable (option)

​	constexpr (option)

​	예외 속성 (option)

​    -> 반환 타입 (option)

{

​	본 문

}

- [=, &b, i{22}, this] () { ... } b를 참조로 담고 this는 복사본으로 담으며, 새로운 변수인 i를 22로 초기화하고 나머지 변수는 복사본으로 담는다.



###### mutable

함수 객체가 복사([=])돼 담긴 변수를 수정할 수 있으려면 mutable을 정의해야 한다. 캡쳐된 객체의 비상수 함수에 대한 호출을 포함한다.

###### constexpr

constexpr로 명시적으로 표시하면 constexpr 함수의 기준을 충족하지 않을 경우 컴파일러 오류를 일으킬 것이다. constexpr 함수와 람다 표현식의 장점은 컴파일러가 해당 결과물을 컴파일하는 동안 컴파일 시간 상수 파라미터로 호출할 수 있을지를 평가할 수 있다는 것이다. 즉, 나중에 바이너리 단계에서 코드의 양이 줄어드는 장점이 있다.

##### 병합을 이용해 함수 구성

```c++
template<typename T, typename ...Ts>
auto concat(T t, Ts ...ts)
{
    // if constexpr은 재귀 단계에서 왼쪽으로 병합되도록 한 개 이상의 함구 있는지를 확인한다.
    if constexpr (sizeof...(ts) > 0)
    {
        return [=](auto ...parameters)
        {
            return t(concat(ts...)(parameters...))
        };
    }
    else
    {
        return t;
    }
}

int main()
{
    auto twice([](int i) { return i * 2;});
    auto thrice([](int i) { return i * 3;});
    
    auto combined( concat(twice, thrice, std::plus<int>{}) );
    
    std::cout << combined(2, 3);
};
```

프로그램을 컴파일하고 실행하면 2 * 3 * (2 + 3) 인 30이 출력된다.