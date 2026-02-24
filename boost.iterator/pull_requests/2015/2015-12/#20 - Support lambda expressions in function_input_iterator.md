# #20 Support lambda expressions in function_input_iterator [Merged]

> Username: nekko1119  
> Created at: 2015-12-27 18:05:19 UTC  
> Updated at: 2016-12-18 16:16:53 UTC  
> Merged at: 2016-12-18 16:16:53 UTC  
> Closed at: 2016-12-18 16:16:53 UTC  
> Url: https://github.com/boostorg/iterator/pull/20  

I want to use lamda expressions in `function_input_iterator`.  
  
Tested under windows 10 64bit with  
- g++ 4.8.1 (`-std=c++03`, `-std=c++11`, `-std=c++1y`, `-std=gnu++03`, `-std=gnu++11`, `-std=gnu++1y`)  
- clang++ 3.7.0 (`-std=c++03`, `-std=c++11`, `-std=c++14`, `-std=c++1z`, `-std=gnu++98`, `-std=gnu++11`, `-std=gnu++14`, `-std=gnu++1z`)  
- msvc 12.0, 14.0

---

## Comment 1

> Username: nekko1119  
> Created_at: 2016-02-03 19:36:09 UTC  
> Url: https://github.com/boostorg/iterator/pull/20#issuecomment-179421628  

Please review this PR :pray:

---

## Comment 2

> Username: faithandbrave  
> Created_at: 2016-02-17 15:09:09 UTC  
> Updated_at: 2016-02-17 15:09:41 UTC  
> Url: https://github.com/boostorg/iterator/pull/20#issuecomment-185244622  

FYI, related discussion:  
[function_output_iterator constructed from a lambda function is not assignable](http://thread.gmane.org/gmane.comp.lib.boost.devel/265402)

---

## Review 3 [Changes requested]

> Username: eldiener  
> Created_at: 2016-12-12 21:53:59 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/iterator/pull/20#pullrequestreview-12558999  

You need to specifically include config.hpp if you use BOOST_DEDUCED_TYPENAME

---
