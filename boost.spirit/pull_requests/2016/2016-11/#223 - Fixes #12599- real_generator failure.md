# #223 Fixes #12599: real_generator failure [Closed]

> Username: inakoll  
> Created at: 2016-11-10 19:58:43 UTC  
> Updated at: 2017-12-18 11:04:52 UTC  
> Closed at: 2017-12-18 11:04:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/223  

```  
#define DBL_MAX_10_EXP 308 // comment this line to get a stack overflow instead of an assertion  
#include <boost/spirit/include/karma.hpp>  
#include <iostream>  
#include <iterator>  
#include <string>  
  
int main() {  
  std::string s;  
  boost::spirit::karma::generate(std::back_insert_iterator<std::string>(s), 1e-309); // any subnormal float would do  
  std::cout << s << std::endl;  
  return 0;  
}  
```

---

## Comment 1

> Username: inakoll  
> Created_at: 2016-11-10 20:06:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/223#issuecomment-259793553  

Fixes [#5983](https://svn.boost.org/trac/boost/ticket/5983) and [#12599](https://svn.boost.org/trac/boost/ticket/12599).  
I have just find out ticket #5953 for which another patch was proposed 3 years ago.

---

## Comment 2

> Username: djowel  
> Created_at: 2016-11-10 20:50:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/223#issuecomment-259803586  

Can you file a PR for that patch?

---

## Comment 3

> Username: inakoll  
> Created_at: 2016-11-10 21:23:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/223#issuecomment-259811323  

Done : https://github.com/boostorg/spirit/pull/224

---

## Comment 4

> Username: octopus-prime  
> Created_at: 2017-12-18 11:04:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/223#issuecomment-352395041  

See https://github.com/boostorg/spirit/pull/328

---
