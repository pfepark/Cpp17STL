# iterator

##### 자신만의 순환 가능한 범위 생성

반복자는 모든 종류의 컨테이너를 순환할 수 있는 표준 인터페이스. 전방 증가 연산자 ++와 참조 연산자 *, 객체 비교 연산자 ==를 구현하기만 하면 C++11 범위 기반의 for 반복문(ranged-for loop)에 알맞은 원시 반복자가 된다.

```c++
// 정수 반복자
class num_iterator
{
    int i;
public:
    explicit num_iterator(int position = 0) : i{position} {}
    
    int operator*() const { return i; }
    num_iterator& operator++()
    {
        ++i;
        return *this;
    }
    
    bool operator!=(const num_iterator& other) const
    {
        return i != other.i;
    }
}

// 시작과 끝 반복자를 포함하는 중개 객체
class num_range
{
    int a;
    int b;
public:
    num_range(int from, int to)
        : a{from}, b{to}
    {}
    
    num_iterator begin() const { return num_iterator{a}; }
    num_iterator end() const { return num_iterator{b}; }
}

int main()
{
    for (int i : num_range{100, 110})
    {
        std::cout << i << ", ";
    }
}
```

##### STL반복자 카테고리와 호환되는 자신만의 반복자 생성

```c++
// 정수 반복자
class num_iterator
{
    int i;
public:
    explicit num_iterator(int position = 0) : i{position} {}
    
    int operator*() const { return i; }
    num_iterator& operator++()
    {
        ++i;
        return *this;
    }
    
    bool operator!=(const num_iterator& other) const
    {
        return i != other.i;
    }
    
    bool operator==(const num_iterator& other) const
    {
        return !(*this != other);
    }
}

int main()
{
    num_range r{100, 110};
    
    // std::minmax_element는 해당 범위에서 가장 작은 값과 가장 큰 값을 가리키는 두개의 요소를
    // std::pair로 반환한다.
    // iterator_traits 관련 컴파일 오류가 발생
    auto [min_it, max_it] (std::minmax_element(std::begin(r), std::end(r)));
    std::cout << *min_it << "-" << *max_it;
}

// std::iterator_traits 템플릿 구조체 특수화를 해야한다.
// 이를 통해 STL은 num_iterator가 전위 반복자 카테고리며 int값을 순환한다는 것을 알게 된다.
namespace std
{
    template<>
    struct iterator_traits<num_iterator> {
    	using iterator_category = std::forward_iterator_tag;
    	using value_type = int;
    	using difference_type = void;
    	using pointer = int*;
    	using reference = int&;
    }
}
```

