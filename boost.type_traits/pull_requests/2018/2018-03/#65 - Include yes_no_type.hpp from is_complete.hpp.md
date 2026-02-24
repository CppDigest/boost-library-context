# #65 Include yes_no_type.hpp from is_complete.hpp [Merged]

> Username: danieljames  
> Created at: 2018-03-14 13:57:38 UTC  
> Updated at: 2018-03-31 10:38:13 UTC  
> Merged at: 2018-03-31 10:38:13 UTC  
> Closed at: 2018-03-31 10:38:13 UTC  
> Url: https://github.com/boostorg/type_traits/pull/65  

Quickbook is failing to build for g++ 4.5 in C++0x mode, with this error:  
  
```  
In file included from ../boost/type_traits/is_default_constructible.hpp:15:0,  
                 from ../boost/type_traits/has_nothrow_constructor.hpp:22,  
                 from ../boost/optional/optional.hpp:38,  
                 from ../boost/optional.hpp:15,  
                 from ../boost/spirit/home/classic/core/match.hpp:15,  
                 from ../boost/spirit/home/classic/core/scanner/scanner.hpp:14,  
                 from ../boost/spirit/home/classic/core/parser.hpp:14,  
                 from ../boost/spirit/home/classic/attribute/parametric.hpp:13,  
                 from ../boost/spirit/home/classic/attribute.hpp:34,  
                 from ../boost/spirit/include/classic_attribute.hpp:11,  
                 from C:\boost\develop\boost_root\tools\quickbook\src\main_grammar.cpp:11:  
../boost/type_traits/is_complete.hpp:62:17: error: 'yes_type' in namespace 'boost::type_traits' does not name a type  
../boost/type_traits/is_complete.hpp:64:10: error: expected unqualified-id before 'template'  
../boost/type_traits/is_complete.hpp:67:43: error: 'check' was not declared in this scope  
../boost/type_traits/is_complete.hpp:67:50: error: expected primary-expression before '>' token  
../boost/type_traits/is_complete.hpp:67:66: error: 'yes_type' is not a member of 'boost::type_traits'  
```  
  
From:  
  
http://www.boost.org/development/tests/develop/developer/output/igaztanaga-develop-gcc-4-5c++03-boost-bin-v2-tools-quickbook-test-anchor-1_1-test-gcc-4-5c+-dbg-cxstd-0x-iso-dbg-symbl-off-lnk-sttc.html  
  
The include isn't needed for all configurations, but the configuration logic is quite complex, so it didn't seem appropriate to repeat it.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-03-31 03:49:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/65#issuecomment-377663315  

Would it not be better if the include were added just for the block of code that needs it, ie. after the line:  
  
#elif !defined(BOOST_NO_SFINAE) && !defined(BOOST_NO_CXX11_FUNCTION_TEMPLATE_DEFAULT_ARGS) && !BOOST_WORKAROUND(BOOST_GCC_VERSION, < 40500)

---

## Comment 2

> Username: danieljames  
> Created_at: 2018-03-31 09:41:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/65#issuecomment-377680513  

If you want, but that is currently inside the boost namespace, so would also need to move the namespace specifications inside of each #if/#else clause.  
  
Also, the cause of this problem is a conditional include, so it feels like it might be setting up another similar problem.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-03-31 10:37:59 UTC  
> Url: https://github.com/boostorg/type_traits/pull/65#issuecomment-377683698  

>Also, the cause of this problem is a conditional include, so it feels like it might be setting up another similar problem.  
  
+1, will merge shortly.

---
