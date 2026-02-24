# #133 - (Question) boost::json::string as key of associative array? [Closed]

> Username: tomasgareau  
> Created at: 2020-08-07 19:47:30 UTC  
> Updated at: 2020-08-09 16:17:27 UTC  
> Closed at: 2020-08-09 16:05:50 UTC  
> Url: https://github.com/boostorg/json/issues/133  

Trying to use boost::json::string as a key in (e.g.,) an unordered map  
  
```  
#include "boost/json/string.hpp"  
  
#include <unordered_map>  
  
int main(int argc, char** argv) {  
  std::unordered_map<boost::json::string, int> map;  
  boost::json::string s = "test";  
  map.insert(s, 10);  
}  
```  
  
fails to compile, giving:  
  
```  
 error: static_assert failed  
      due to requirement '__check_hash_requirements<string, hash<string> >::value' "the specified hash  
      does not meet the Hash requirements"  
```  
  
This is easy enough to work around by converting to e.g., std::string but is a bit awkward. I'm fairly new to some of the rationale & concepts behind boost::json::string as [described in the docs](http://vinniefalco.github.io/doc/json/json/usage/strings.html) and so was wondering: is there is a reason behind this/a different intended way to use strings as keys/some other explanation?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-08 00:07:12 UTC  
> Url: https://github.com/boostorg/json/issues/133#issuecomment-670791176  

The library needs to provide a specialization of `std::hash` for `json::string`. It will be done, thanks!

---

## Comment 2

> Username: tomasgareau  
> Created at: 2020-08-09 16:12:49 UTC  
> Url: https://github.com/boostorg/json/issues/133#issuecomment-671070584  

There's a turnaround to write home about :) Thanks!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-09 16:17:27 UTC  
> Url: https://github.com/boostorg/json/issues/133#issuecomment-671071244  

Sorry it took so long, we ran into a bug in libstdc 4.8 :)
