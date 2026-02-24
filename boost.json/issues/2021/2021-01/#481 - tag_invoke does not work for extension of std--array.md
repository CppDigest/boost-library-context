# #481 - tag_invoke does not work for extension of std::array [Closed]

> Username: pmconrad  
> Created at: 2021-01-08 15:47:55 UTC  
> Updated at: 2021-01-12 23:10:43 UTC  
> Closed at: 2021-01-12 23:10:43 UTC  
> Url: https://github.com/boostorg/json/issues/481  

### Version of Boost  
  
I'm using the standalone version tagged as 1.75.0 https://github.com/boostorg/json/commit/edbf86641b7217676eceb6582ee486e31bb42745  
  
### Steps necessary to reproduce the problem  
  
```  
#define BOOST_JSON_STANDALONE  
  
#include <boost/json/src.hpp>  
#include <boost/json/parse.hpp>  
#include <boost/json/serialize.hpp>  
#include <boost/json/value.hpp>  
#include <boost/json/value_from.hpp>  
#include <boost/json/value_to.hpp>  
  
#include <iostream>  
#include <sstream>  
  
namespace test {  
  
template<std::size_t N>  
class zeroed_array : public std::array<unsigned char, N> {  
public:  
    zeroed_array() : std::array<unsigned char, N>({0}) {}  
};  
  
template<std::size_t N>  
void tag_invoke(const boost::json::value_from_tag&, boost::json::value& v, const zeroed_array<N>& a) {  
    v = "hello world";  
}  
  
template<std::size_t N>  
zeroed_array<N> tag_invoke(const boost::json::value_to_tag<zeroed_array<N>>&, const boost::json::value& v) {  
    zeroed_array<N> res;  
    res[0] = '1';  
    return res;  
}  
  
}  
  
int main() {  
    test::zeroed_array<5> tmp;  
    tmp[0] = '2';  
    std::cout << boost::json::value_from(tmp) << "\n";  
    tmp = boost::json::value_to<test::zeroed_array<5>>(boost::json::parse("\"hello world\""));  
    std::cout << tmp[0] << "\n";  
}  
```  
  
Put the above code into `/tmp/test.cpp` and compile with  
```  
clang++ --std=c++17 -o /tmp/test -I boost-json/include/ /tmp/test.cpp   
```  
  
Then run `/tmp/test`. Actual output is  
```  
[50,0,0,0,0]  
1  
```  
  
Expected output is  
```  
hello world  
1  
```  
  
For some reason, `template<std::size_t N>  
void tag_invoke(const boost::json::value_from_tag&, boost::json::value& v, const zeroed_array<N>& a)` is not used, but the generic implementation is used instead. I haven't been able to figure out why. Interestingly, the other tag_invoke (with `value_to_tag`) is being used, as the second line of output shows.  
  
Since `value_from_generic` is not specific to `std::array`, I expect that similar problems occur for other base classes.  
  
### All relevant compiler information  
```  
> clang++ --version  
clang version 9.0.1   
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
Same with  
```  
> g++-9 --version  
g++-9 (SUSE Linux) 9.3.1 20200406 [revision 6db837a5288ee3ca5ec504fbd5a765817e556ac2]  
Copyright (C) 2019 Free Software Foundation, Inc.  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-01-08 22:11:42 UTC  
> Url: https://github.com/boostorg/json/issues/481#issuecomment-757024485  

Investigating...
