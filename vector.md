# vector

##### 삭제-제거
```c++
// 벡터에서 모든 2값을 제거하는 코드
std::vector<int> v{ 1,2,3,2,5,2,6,2,4,8 };

const auto new_end(std::remove(std::begin(v), std::end(v), 2));
v.erase(new_end, std::end(v));
```

##### 순서 상관없이 빠른 삭제
```c++
// 마지막 값을 받아 삭제하려는 요소에 덮어쓰고 마지막 요소를 제거함.
template<typename T>
void quick_remove_at(std::vector<T>& v, std::size_t idx)
{
    if (idx < v.size())
    {        
        v[idx] = std::move(v.back());
        v.pop_back();
    }
}

template<typename T>
void quick_remove_at(std::vector<T>& v, typename std::vector<T>::iterator it)
{
    if (it != std::end(v))
    {
        *it = std::move(v.back());
        v.pop_back();
    }
}
```

##### 안전한 접근

```c++
try
{
    // []연산자와 다르게 at함수는 범위 확인을 하고 범위밖에 대한 접근은 예외를 발생한다.
    std::cout << "Out of range element value: " << v.at(container_size + 10) << '\n';
}
catch(const std::out_of_range& e)
{
    std::cout << "Oops, out of range access detected: " << e.what() << '\n';
}
```

##### 정렬 유지

```c++
// 이미 정렬된 백터라는 가정하에 삽입시 정렬을 유지한다. 
void insert_sorted(vector<string>& v, const string& word)
 {
     // word보다 크거나 같은 첫 번째 요소를 찾아내 이를 가리키는 반복자를 반환
     const auto insert_pos(lower_bound(begin(v), end(v), word));
     v.insert(insert_pos, word);
 }
```

