# #63 Fix compile errors on gcc 4.4 C++0x mode [Closed]

> Username: danieljames  
> Created at: 2018-02-07 17:45:39 UTC  
> Updated at: 2018-02-07 18:09:00 UTC  
> Closed at: 2018-02-07 18:04:43 UTC  
> Url: https://github.com/boostorg/type_traits/pull/63  

While building quickbook, I'm getting compile errors for gcc 4.4 in C++0x mode. This is my quick attempt at fixing them, although the test still fails. Easiest solution might be to not use the C++11 features on this compiler in this case.  
  
You can see the compile error at https://travis-ci.org/boostorg/quickbook/jobs/338435551:  
  
```  
In file included from ../../../boost/type_traits/is_default_constructible.hpp:15,  
                 from ../../../boost/type_traits/has_nothrow_constructor.hpp:22,  
                 from ../../../boost/optional/optional.hpp:38,  
                 from ../../../boost/optional.hpp:15,  
                 from ../../../boost/spirit/home/classic/core/match.hpp:15,  
                 from ../../../boost/spirit/home/classic/core/scanner/scanner.hpp:14,  
                 from ../../../boost/spirit/home/classic/core/parser.hpp:14,  
                 from ../../../boost/spirit/home/classic/core/primitives/primitives.hpp:15,  
                 from ../../../boost/spirit/include/classic_primitives.hpp:11,  
                 from /home/travis/boost/tools/quickbook/test/src/text_diff.cpp:15:  
../../../boost/type_traits/is_complete.hpp:60: error: ‘declval’ is not a member of ‘std’  
../../../boost/type_traits/is_complete.hpp:60: error: expected primary-expression before ‘>’ token  
../../../boost/type_traits/is_complete.hpp:60: error: expected primary-expression before ‘)’ token  
../../../boost/type_traits/is_complete.hpp:61: error: expected initializer before ‘check’  
../../../boost/type_traits/is_complete.hpp:64: error: expected initializer before ‘check’  
../../../boost/type_traits/is_complete.hpp:66: error: ‘check’ was not declared in this scope  
../../../boost/type_traits/is_complete.hpp:66: error: expected primary-expression before ‘>’ token  
../../../boost/type_traits/is_complete.hpp:66: error: ‘yes_type’ is not a member of ‘boost::type_traits’  
```

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-02-07 18:04:43 UTC  
> Url: https://github.com/boostorg/type_traits/pull/63#issuecomment-363856781  

I've pushed a couple of commits that fix this slightly differently - basically just disable is_complete for gcc < 4.5 which I feel might be safer.  Also fixed the namespace SNAFU on integral_constant.

---

## Comment 2

> Username: danieljames  
> Created_at: 2018-02-07 18:09:00 UTC  
> Url: https://github.com/boostorg/type_traits/pull/63#issuecomment-363858162  

That sounds good. Thanks for fixing this.

---
