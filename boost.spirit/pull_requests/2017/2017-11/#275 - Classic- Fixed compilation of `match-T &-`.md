# #275 Classic: Fixed compilation of `match<T &>` [Merged]

> Username: Kojoley  
> Created at: 2017-11-12 14:12:53 UTC  
> Updated at: 2018-02-19 22:55:45 UTC  
> Merged at: 2017-11-12 14:13:04 UTC  
> Closed at: 2017-11-12 14:13:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/275  

`boost::optional<T &>` has member named `argument_type`  
  
```  
In file included from ../libs/spirit/classic/test/match_tests.cpp:15:  
In file included from ../boost/spirit/include/classic_core.hpp:11:  
In file included from ../boost/spirit/home/classic/core.hpp:28:  
../boost/spirit/home/classic/core/match.hpp:69:41: error: no type named 'argument_type' in 'boost::optional<int &>'  
        typedef typename optional_type::argument_type ctor_param_t;  
                ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~  
../libs/spirit/classic/test/match_tests.cpp:75:17: note: in instantiation of template class 'boost::spirit::classic::match<int &>' requested here  
    match<int&> mr;  
                ^  
1 error generated.  
```  
  
I do not know when it did break because defining `BOOST_OPTIONAL_CONFIG_USE_OLD_IMPLEMENTATION_OF_OPTIONAL` does not solve the problem.

---
