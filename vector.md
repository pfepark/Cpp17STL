# vector

##### 삭제-제거
```c++
// 벡터에서 모든 2값을 제거하는 코드
std::vector<int> v{ 1,2,3,2,5,2,6,2,4,8 };

const auto new_end(std::remove(std::begin(v), std::end(v), 2));
v.erase(new_end, std::end(v));
```