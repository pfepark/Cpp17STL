# std::map

##### 필요한 조건을 걸어 효율적으로 요소 삽입

```c++
struct billionaire
{
    string name;
    double dollars;
    string country;
}

std::list<billionaire> billionaires {
    { "Bill Gates", 86.0, "USA"},
    { "Warren Buffet", 75.6, "USA"},
    { "Amancio Ortega", 71.3, "Spain"},
    { "Carlos Slim", 54.5, "Mexico"},
}

std::map<std::string, std::pair<const billionaire, size_t>> m;
for (const auto&b : billionaires)
{
    // 삽입에 성공하면  true와 새로운 노드를 가르키는 반복자를 리턴,
    //       실패하면 false와 충돌된 요소를 가리키는 반복자를 리턴.
    auto [iterator, success] = m.try_emplace(b.country, b, 1);
    if (!success)
    {
        // 이미 존재하는 경우 해당 키와 관련된 객체를 생성하지 않는 점이 다르다. 객체 생성비용 절약.
        iterator->second.second += 1;
    }
}
```

##### 요소의 키를 효율적으로 변경

```c++
// https://en.cppreference.com/w/cpp/container/map/extract
std::map<int, string> race {
    {1,"mario"}, {2, "lugi"}, {3, "bowser"}, {4, "peach"}, {5, "yoshi"}, {6, "koopa"}
};

auto a (race.extract(3));		// node_type으로 추출되어 map에서 빠진다.
auto b (race.extract(5));

std::swap(a.key(), b.key());	// node간에 key를 swap하고

race.insert(std::move(a));		// 다시 맵에 삽입하면 된다.
race.insert(std::move(b));
```

