# #394 - Qi: Attributeless parsers within container handling parsers [Open]

> Username: Kojoley  
> Created at: 2018-08-26 22:41:51 UTC  
> Updated at: 2020-05-31 11:46:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/394  

Update: https://svn.boost.org/trac10/ticket/6444 looks like the same problem.  
  
X3 appends a default constructed value to containers if underlying parser of container handling parser (such as `kleene`, `plus` or `list`) does not generate anything.  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/qi.hpp>  
#include <vector>  
#include <iostream>  
  
template <typename F>  
void test(F f)  
{  
    std::vector<int> v;  
    char s[] = "1\n2\n3\n---\n5\n---\n---\n7\n---";  
    f(std::begin(s), std::end(s), v);  
    for (auto x : v) std::cout << x << '\n';  
}  
  
int main()  
{  
    namespace qi = boost::spirit::qi;  
    namespace x3 = boost::spirit::x3;  
    std::cout << "qi:\n"; test([](auto f, auto l, auto& v){ qi::parse(f, l, (qi::int_ | "---") % qi::eol, v); });  
    std::cout << "x3:\n"; test([](auto f, auto l, auto& v){ x3::parse(f, l, (x3::int_ | "---") % x3::eol, v); });  
}  
```  
  
Output:  
```  
qi:  
1  
2  
3  
5  
7  
x3:  
1  
2  
3  
0  
5  
0  
0  
7  
0  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-08-26 22:59:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/394#issuecomment-416079222  

Oh god, there is inconsistency even in Qi itself.  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/qi.hpp>  
#include <boost/fusion/include/std_tuple.hpp>  
#include <vector>  
#include <iostream>  
  
template <typename F>  
void test(F f)  
{  
    std::vector<std::tuple<int, char>> v;  
    char s[] = "1:a\n2:b\n3:c\n---\n5:e\n---\n---\n7:h\n---";  
    f(std::begin(s), std::end(s), v);  
    for (auto [x, y] : v) std::cout << x << ':' << y << '\n';  
}  
  
int main()  
{  
    namespace qi = boost::spirit::qi;  
    namespace x3 = boost::spirit::x3;  
    std::cout << "qi:\n"; test([](auto f, auto l, auto& v) { qi::parse(f, l, (qi::int_ >> ':' >> qi::char_ | "---") % qi::eol, v); });  
    std::cout << "x3:\n"; test([](auto f, auto l, auto& v) { x3::parse(f, l, (x3::int_ >> ':' >> x3::char_ | "---") % x3::eol, v); });  
}  
```  
Output:  
```  
qi:  
1:a  
2:b  
3:c  
0:  
5:e  
0:  
0:  
7:h  
0:  
x3:  
1:a  
2:b  
3:c  
0:  
5:e  
0:  
0:  
7:h  
0:  
```

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-08-26 23:23:21 UTC  
> Updated at: 2018-08-26 23:48:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/394#issuecomment-416080562  

This is completely mind-blowing:  
```cpp  
#include <boost/spirit/home/qi.hpp>  
#include <boost/fusion/include/std_tuple.hpp>  
#include <vector>  
#include <iostream>  
  
template <typename F>  
void test(F f)  
{  
    std::vector<std::tuple<int, char>> v;  
    char s[] = "1:a\n2:b\n3:c\n---\n5:e\n---\n---\n7:h\n---";  
    f(std::begin(s), std::end(s), v);  
    for (auto [x, y] : v) std::cout << ' ' << x << ':' << y;  
    std::cout << '\n';  
}  
  
int main()  
{  
    namespace qi = boost::spirit::qi;  
    std::cout << "#1:"; test([](auto f, auto l, auto& v) {  
        qi::parse(f, l, ((qi::int_ >> ':' >> qi::char_) | "---") % qi::eol, v);  
    });  
    std::cout << "#2:"; test([](auto f, auto l, auto& v) {  
        qi::parse(f, l, (-(qi::int_ >> ':' >> qi::char_) >> -qi::lit("---")) % qi::eol, v);  
    });  
    // does not compile for some reason  
    //std::cout << "#3:"; test([](auto f, auto l, auto& v) {  
    //    qi::parse(f, l, (-(qi::int_ >> ':' >> qi::char_) >> -qi::lit("---") | qi::eps) % qi::eol, v);  
    //});  
    std::cout << "#3:"; test([](auto f, auto l, auto& v) {  
        qi::rule<decltype(f), std::tuple<int, char>> item = (qi::int_ >> ':' >> qi::char_);  
        qi::parse(f, l, (-item >> -qi::lit("---") | qi::eps) % qi::eol, v);  
    });  
    // does not compile for some reason  
    //std::cout << "#4:\n"; test([](auto f, auto l, auto& v) {  
    //    qi::parse(f, l, ((qi::int_ >> ':' >> qi::char_) || "---") % qi::eol, v);  
    //});  
    std::cout << "#4:"; test([](auto f, auto l, auto& v) {  
        qi::rule<decltype(f), std::tuple<int, char>> item = (qi::int_ >> ':' >> qi::char_);  
        qi::parse(f, l, (item || "---") % qi::eol, v);  
    });  
}  
```  
Output:  
```  
#1: 1:a 2:b 3:c 0:  5:e 0:  0:  7:h 0:  
#2: 1:a 2:b 3:c 0:  5:e 0:  0:  7:h 0:  
#3: 1:a 2:b 3:c 5:e 7:h  
#4: 1:a 2:b 3:c 5:e 7:h  
```

---

## Comment 3

> Username: djowel  
> Created at: 2018-08-27 00:55:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/394#issuecomment-416086463  

That's a horrendous bug!

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-11-22 15:03:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/394#issuecomment-441056323  

https://svn.boost.org/trac10/ticket/6444 looks like the same problem.

---

## Comment 5

> Username: Kojoley  
> Created at: 2020-05-31 11:45:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/394#issuecomment-636460082  

I am reopening it and retargeting to Qi
