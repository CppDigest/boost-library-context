# #35 Fix assignment basic_hold_any::operator= [Merged]

> Username: sehe  
> Created at: 2014-06-05 22:53:33 UTC  
> Updated at: 2014-06-26 22:19:14 UTC  
> Merged at: 2014-06-05 23:01:15 UTC  
> Closed at: 2014-06-05 23:01:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/35  

The template version would not be used because the compiler-generated  
one was a closer match. The implicit assignment, however, broke the  
ownership semantics.  
  
This case was inspired by this SO question: http://stackoverflow.com/q/24065769/85371  
  
A smaller reproducer is here:  
  
```  
#include <iostream>  
#include <string>  
#include <boost/spirit/home/support/detail/hold_any.hpp>  
typedef boost::spirit::hold_any any;  
  
int main()  
{  
    any b;  
    {  
        any a;  
        a = std::string("test_test_test");  
        b = a;  
    }  
  
    std::cout << "b: " << b << '\n';  
}  
```  
  
This crashes with gcc/clang regardless of c++03/c++11 modes. It doesn't crash and runs valngrind-clean after the patch.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-06-05 23:00:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/35#issuecomment-45285856  

Please target the develop branch instead.

---

## Comment 2

> Username: djowel  
> Created_at: 2014-06-05 23:01:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/35#issuecomment-45285927  

ah, no, forget it. i'll merge to both  branches. this looks simple enough.

---

## Comment 3

> Username: sehe  
> Created_at: 2014-06-05 23:34:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/35#issuecomment-45288147  

Mmm. I just realized that this fix would be enough under c++03, but more is required under c++11, since the same danger lurks with move-semantics. How do we usually provide c++11 specific code in the Spirit V2 code base?

---

## Comment 4

> Username: djowel  
> Created_at: 2014-06-05 23:39:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/35#issuecomment-45288462  

Same as what you did, but please target develop branch instead.

---
