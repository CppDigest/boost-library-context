# #301 - X3 doesn't compile on g++ 4.8 cxxstd=11 [Closed]

> Username: pdimov  
> Created at: 2017-11-28 14:45:28 UTC  
> Updated at: 2017-11-29 10:50:34 UTC  
> Closed at: 2017-11-29 10:50:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/301  

https://travis-ci.org/boostorg/boost/jobs/308195225  
  
```  
./boost/spirit/home/x3/support/context.hpp:28:18: error: expected primary-expression before ‘auto’  
         decltype(auto) get(ID_ id) const  
                  ^  
```  
  
These tests should probably be guarded by `requires`.  
  
Also,  
  
```  
In file included from libs/spirit/test/x3/x3_variant.cpp:9:  
./boost/spirit/home/x3/support/ast/variant.hpp:134:50: error: no template named 'remove_reference_t' in namespace 'std'; did you mean simply 'remove_reference_t'?  
                                                ,std::remove_reference_t<T>  
```  
  
Looks like X3 wants C++14.

---

## Comment 1

> Username: Kojoley  
> Created at: 2017-11-28 14:49:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/301#issuecomment-347546679  

Yeah, X3 requires c++14. I do not know why https://github.com/boostorg/spirit/blob/0917fca9fdcf3b42a3f5dedd595217164d94a3fb/test/x3/Jamfile#L17-L24 is not working, I did not touch this and thought it worked.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-11-28 14:57:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/301#issuecomment-347549098  

That's not the correct way to do it even if it worked, as it would've overridden `cxxstd=17` and you do want to be able to test on C++17; use `[ requires cxx14_something ]`.

---

## Comment 3

> Username: Kojoley  
> Created at: 2017-11-28 15:00:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/301#issuecomment-347550085  

Yes, this was my plan. I just actually turned on x3 tests to see a feedback from regression matrix while knew that x3 tests not in a good condition.

---

## Comment 4

> Username: djowel  
> Created at: 2017-11-28 22:41:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/301#issuecomment-347688623  

That was a really old commit, before gcc even had std=c++14, IIRC. Yes, X3 requires at least C++14.
