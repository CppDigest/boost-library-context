# #104 - Named arg defaulted when supplied [Closed]

> Username: sehe  
> Created at: 2020-09-03 20:29:26 UTC  
> Updated at: 2021-12-08 15:44:05 UTC  
> Closed at: 2021-12-08 15:44:04 UTC  
> Url: https://github.com/boostorg/parameter/issues/104  

When supplying a default in the indexer keyword, the result is default when it shouldn't be:  
  
**[Live On Coliru](http://coliru.stacked-crooked.com/a/d13f22372a0c7c8b)**  
  
```c++  
#include <boost/log/utility/setup/file.hpp>  
#include <iostream>  
  
int main() {  
    namespace kw = boost::log::keywords;  
    auto test = [](auto params) {  
        std::cerr << "Non-defaulted: " << params[kw::file_name] << std::endl;  
        std::cerr << "Defaulted:     " << params[kw::file_name | boost::filesystem::path()] << std::endl;  
    };  
  
    test((kw::file_name = "aaa"));  
    test((kw::file_name = "bbb", kw::auto_flush = false));  
    test((kw::auto_flush = false, kw::file_name = "ccc"));  
}  
```  
  
Prints:  
  
```  
Non-defaulted: aaa  
Defaulted:     ""  
Non-defaulted: bbb  
Defaulted:     bbb  
Non-defaulted: ccc  
Defaulted:     ccc  
```

---

## Comment 1

> Username: sehe  
> Created at: 2020-09-03 20:32:03 UTC  
> Url: https://github.com/boostorg/parameter/issues/104#issuecomment-686749779  

Close inspection shows that the fact that the default value is rvalue makes a difference:  
  
```c++  
        using boost::filesystem::path;  
        auto args = (kw::file_name = "aaa");  
        path lvalue;  
        std::cerr << "default_r_<>: " << args[kw::file_name | path()] << "\n";  
        std::cerr << "default_<>: "   << args[kw::file_name | lvalue] << "\n";  
```  
  
Prints  
  
````  
    default_r_<>: ""  
    default_<>: aaa  
```  
  
There appears to be a missing overload for` default_r_<keytype, Default>` as there is in `tagged_argument_rref`.

---

## Comment 2

> Username: sehe  
> Created at: 2020-09-03 20:45:17 UTC  
> Url: https://github.com/boostorg/parameter/issues/104#issuecomment-686755736  

I was going to create a PR, so I opened [issue #104](https://github.com/boostorg/parameter/issues/104). But when forking is turns out that there is already a fix in develop:  
  
    tree fff523c8fe0a3de1c1378a2292840f217cc4d6d3  
    parent 0f548424a5f966fadfa7a21a759c835729cbc009  
    author Andrey Semashev <andrey.semashev@gmail.com> Sun Mar 15 18:13:07 2020 +0300  
    committer Andrey Semashev <andrey.semashev@gmail.com> Sun Mar 15 18:20:34 2020 +0300  
      
    Fix argument value selection with an rvalue default.  
      
    In C++11 mode, when named parameter pack was a single tagged argument,  
    parameter value was not extracted when an rvalue default value was  
    provided by the user (instead, the default value was returned). This  
    commit adds a missing overload for default_r_, which returns the parameter  
    value.  
      
    Fixes https://github.com/boostorg/parameter/issues/97.  
  
So, yeah look like the fix is months old and the issue duplicates #97.

---

## Comment 3

> Username: sehe  
> Created at: 2020-09-03 20:45:20 UTC  
> Url: https://github.com/boostorg/parameter/issues/104#issuecomment-686755761  

This is a shame, because I spent at least 2 hours on analyzing this for https://stackoverflow.com/questions/63718805/parameter-passing-bug-in-boost-log/63727877#63727877  
  
Can we improve the responsiveness on merge requests? In particular, why is develop not merged? Or has it been reverted? I don't follow, but I've also found https://github.com/boostorg/parameter/issues/97 which mentions #97 and "reverting to 1.70.0".

---

## Comment 4

> Username: Lastique  
> Created at: 2020-09-04 06:20:28 UTC  
> Url: https://github.com/boostorg/parameter/issues/104#issuecomment-686938115  

I think this is a duplicate of https://github.com/boostorg/parameter/issues/97.

---

## Comment 5

> Username: Lastique  
> Created at: 2021-12-08 15:44:04 UTC  
> Url: https://github.com/boostorg/parameter/issues/104#issuecomment-988928942  

Closing as a duplicate.
