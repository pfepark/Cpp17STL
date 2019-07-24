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

##### std::unordered_map을 사용자 지정 타입으로 사용

```c++
struct coord {
    int x;
    int y;
}

// 사용자 정의 타입에는 비교 연산자 정의와 해시 함수가 필요하다.
bool operator==(const coord& l, const coord& r)
{
    return l.x == r.x && l.y == r.y;
}

// std namespace를 열어 자신만의 특수화된 std::hash 템플릿 구조체를 생성한다.
namespace std
{
    template<>
    struct hash<coord>
    {
        using argument_type = coord;
        using result_type = size_t;
        
        result_type operator()(const argument_type& c) const
        {
            // 예제이므로 굉장한 약식으로 만들어졌다.
            // https://stackoverflow.com/questions/17016175/c-unordered-map-using-a-custom-class-type-as-the-key 를 참고하자.
            return static_cast<result_type>(c.x) + static_cast<result_type>(c.y);
        }
    }
}
```

##### 중복된 사용자 입력 검출 및 sdt::set을 이용한 알파벳순 출력

```c++
using namespace std;
int main()
{
    set<std::string> s;
    
    std::istream_iterator<string> it{cin};
    std::istream_iterator<string> end;
    
    std::copy(it, end, inserter(s, s.end()));
    
    // std::set의 내용을 출력.
}
```

##### std::map을 이용해 단어 빈도수 구현

```c++
// 덧붙여진 쉼표, 마침표, 콜론을 단어로부터 분리하는 헬퍼함수
std::string filter_punctuation(const std::string& s)
{
    const char* forbidden {".,:;"};
    const auto idx_start(s.find_first_not_of(forbidden));
    const auto idx_end(s.find_last_not_of(forbidden));
    return s.substr(idx_start, idx_end - idx_start + 1);
}

int main()
{
    std::map<std::string, size_t> words;
    int max_word_len {0};
    
    std::string s;
    while (std::cin >> s)
    {
        // 입력된 단어에서 쉼표, 마침표, 콜론등을 분리
        auto filtered(filter_punctutaion(s));
        
        // 해당 단어가 지금까지 나온 단어 중 가징 긴 경우 max_word_len 변수를 갱신
        // (출력시 들여쓰기를 위한 변수)
        max_word_len = std::max(max_word_len, filtered.length());
        
        // 맵안에서 단어의 카운터 값을 증가
        ++words[filtered];
    }
    
    // 알파벳순으로 정렬되어 있는 map을 빈도수의 내림차순으로 정렬
    std::vector<std::pair<std::string, size_t>> word_counts;
    word_counts.reserve(words.size());
    std::move(std::begin(words), std::end(words), std::back_inserter(word_counts));
    
    std::sort(std::begin(words_counts), std::end(word_counts),
              [](const auto& a, const auto& b)
              {
                 return a.second > b.second; 
              });
    
    // 들여쓰기를 위한 std::setw 사용
    std::cout <<"# " << std::setw(max_word_len) << "<WORD>" << " #<COUNT>\n";
    for(const auto& [word, count] : word_counts)
    {
        std::cout << std::setw(max_word_len + 2) << word << " #" << count << 'n';
    }
}
```

