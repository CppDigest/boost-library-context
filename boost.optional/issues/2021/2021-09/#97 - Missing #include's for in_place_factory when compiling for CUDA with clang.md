# #97 - Missing #include's for in_place_factory when compiling for CUDA with clang [Open]

> Username: jzmaddock  
> Created at: 2021-09-27 18:25:46 UTC  
> Updated at: 2026-02-20 15:24:40 UTC  
> Url: https://github.com/boostorg/optional/issues/97  

I'm not sure if this is an optional or Boost.Format issue, but I suspect this belongs here.  
  
The issue is that some optional methods use `in_place_factory` as an argument to `is_base_of` which absolutely requires a complete type as argument, but so far as I can see `in_place_factory` is only ever forward declared.  
  
When compiling for CUDA with clang this leads to:  
  
```  
In file included from <source>:14:  
In file included from /opt/compiler-explorer/libs/boost_1_77_0/boost/format.hpp:38:  
In file included from /opt/compiler-explorer/libs/boost_1_77_0/boost/format/internals.hpp:21:  
In file included from /opt/compiler-explorer/libs/boost_1_77_0/boost/optional.hpp:15:  
In file included from /opt/compiler-explorer/libs/boost_1_77_0/boost/optional/optional.hpp:43:  
In file included from /opt/compiler-explorer/libs/boost_1_77_0/boost/type_traits/is_base_of.hpp:12:  
/opt/compiler-explorer/libs/boost_1_77_0/boost/type_traits/is_base_and_derived.hpp:142:25: error: invalid application of 'sizeof' to an incomplete type 'boost::in_place_factory_base'  
    BOOST_STATIC_ASSERT(sizeof(B) != 0);  
                        ^~~~~~~~~  
/opt/compiler-explorer/libs/boost_1_77_0/boost/static_assert.hpp:70:55: note: expanded from macro 'BOOST_STATIC_ASSERT'  
#     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
                                                      ^~~~~~~~~~~  
/opt/compiler-explorer/libs/boost_1_77_0/boost/type_traits/is_base_and_derived.hpp:217:41: note: in instantiation of template class 'boost::detail::is_base_and_derived_impl2<boost::in_place_factory_base, std::locale>' requested here  
    BOOST_STATIC_CONSTANT(bool, value = bound_type::value);  
                                        ^  
/opt/compiler-explorer/libs/boost_1_77_0/boost/type_traits/is_base_of.hpp:25:31: note: in instantiation of template class 'boost::detail::is_base_and_derived_impl<boost::in_place_factory_base, std::locale>' requested here  
            (::boost::detail::is_base_and_derived_impl<ncvB,ncvD>::value) ||  
                              ^  
/opt/compiler-explorer/libs/boost_1_77_0/boost/type_traits/is_base_of.hpp:31:58: note: in instantiation of template class 'boost::detail::is_base_of_imp<boost::in_place_factory_base, std::locale>' requested here  
      : public integral_constant<bool, (::boost::detail::is_base_of_imp<Base, Derived>::value)> {};  
                                                         ^  
/opt/compiler-explorer/libs/boost_1_77_0/boost/optional/optional.hpp:794:32: note: in instantiation of template class 'boost::is_base_of<boost::in_place_factory_base, std::locale>' requested here  
  : boost::conditional< boost::is_base_of<boost::in_place_factory_base, BOOST_DEDUCED_TYPENAME boost::decay<U>::type>::value  
                               ^  
/opt/compiler-explorer/libs/boost_1_77_0/boost/optional/optional.hpp:823:25: note: in instantiation of template class 'boost::optional_detail::is_convertible_to_T_or_factory<std::locale, const std::locale &>' requested here  
  : boost::conditional< is_convertible_to_T_or_factory<T, U>::value  
                        ^  
/opt/compiler-explorer/libs/boost_1_77_0/boost/core/enable_if.hpp:41:41: note: in instantiation of template class 'boost::optional_detail::is_optional_val_init_candidate<std::locale, const std::locale &, false>' requested here  
  struct enable_if : public enable_if_c<Cond::value, T> {};  
                                        ^  
/opt/compiler-explorer/libs/boost_1_77_0/boost/optional/optional.hpp:951:53: note: in instantiation of template class 'boost::enable_if<boost::optional_detail::is_optional_val_init_candidate<std::locale, const std::locale &, false>, bool>' requested here  
                      BOOST_DEDUCED_TYPENAME boost::enable_if< optional_detail::is_optional_val_init_candidate<T, Expr>, bool>::type = true  
                                                    ^  
/opt/compiler-explorer/libs/boost_1_77_0/boost/format/format_implementation.hpp:40:44: note: while substituting deduced template arguments into function template 'optional' [with Expr = const std::locale &]  
          exceptions_(io::all_error_bits), loc_(loc)  
                                           ^  
/opt/compiler-explorer/libs/boost_1_77_0/boost/optional/detail/optional_factory_support.hpp:32:9: note: forward declaration of 'boost::in_place_factory_base'  
  class in_place_factory_base ;  
```  
  
You can see the issue here: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:cuda,selection:(endColumn:23,endLineNumber:9,positionColumn:23,positionLineNumber:9,selectionStartColumn:23,selectionStartLineNumber:9,startColumn:23,startLineNumber:9),source:'%23include+%3Cboost/config.hpp%3E%0A//%0A//+These+%23undefs+reflect+the+current+state+of+Boost.Config+develop+compared+to+1.77:%0A//%0A%23undef+BOOST_NO_CXX11_VARIADIC_TEMPLATES%0A%23undef+BOOST_NO_VARIADIC_TEMPLATES%0A//%0A//+These+includes+are+required+to+make+this+compile,%0A//+uncomment+to+remove+the+errors:%0A//%0A//%23include+%3Cboost/utility/in_place_factory.hpp%3E%0A//%23include+%3Cboost/utility/typed_in_place_factory.hpp%3E%0A%0A%23include+%3Cboost/format.hpp%3E%0A'),l:'5',n:'0',o:'CUDA+C%2B%2B+source+%231',t:'0')),k:63.22701688555347,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((g:!((h:compiler,i:(compiler:cuclang1100,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:cuda,libs:!(),options:'-std%3Dc%2B%2B14+-O3+-I/opt/compiler-explorer/libs/boost_1_77_0/',selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1,tree:'1'),l:'5',n:'0',o:'clang+11.0.0+sm_75+CUDA-10.2+(CUDA+C%2B%2B,+Editor+%231,+Compiler+%231)',t:'0')),header:(),k:43.667751209994,l:'4',m:50,n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compiler:1,editor:1,fontScale:12,fontUsePx:'0',wrap:'1'),l:'5',n:'0',o:'Output+of+clang+11.0.0+sm_75+CUDA-10.2+(Compiler+%231)',t:'0')),header:(),l:'4',m:50,n:'0',o:'',s:0,t:'0')),k:36.77298311444653,l:'3',n:'0',o:'',t:'0')),l:'2',m:100,n:'0',o:'',t:'0')),version:4  
  
Adding   
  
```  
#include <boost/utility/in_place_factory.hpp>  
#include <boost/utility/typed_in_place_factory.hpp>  
```  
  
Before including optional.hpp fixes the issue.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-09-27 18:26:27 UTC  
> Url: https://github.com/boostorg/optional/issues/97#issuecomment-928160186  

This is moved from https://github.com/boostorg/config/issues/406

---

## Comment 2

> Username: psalz  
> Created at: 2021-09-28 08:11:57 UTC  
> Url: https://github.com/boostorg/optional/issues/97#issuecomment-928959573  

Ah yes, I remember running into this a couple of years ago as well. The problem is not actually in Boost.Optional, but rather stems from the fact that not all Boost libraries solely rely on Boost.Config for compiler detection (i.e., some roll their own). So even with the changes in [boostorg/config#297](https://github.com/boostorg/config/issues/297) (great to finally see some movement there!) other places still need to be updated to either check for `__NVCC__` instead of `__CUDACC__`, or, preferably, use only macros set by Boost.Config.  
  
In this case the problem is a check in Boost.TypeTraits' [intrinsic.hpp on line 163](https://github.com/boostorg/type_traits/blob/bfce306637e2a58af4b7bbc881a919dafb7d195b/include/boost/type_traits/intrinsics.hpp#L163). I suspect there are several other Boost libraries with the same problem.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-09-30 09:32:35 UTC  
> Url: https://github.com/boostorg/optional/issues/97#issuecomment-931137198  

>In this case the problem is a check in Boost.TypeTraits' intrinsic.hpp on line 163. I suspect there are several other Boost libraries with the same problem.  
  
That is "a" problem, not "the" problem.  I'll get to that shortly, but it is still a requirement that arguments to `is_base_of` are complete types even if we use an intrinsic to get the answer.

---

## Comment 4

> Username: psalz  
> Created at: 2021-10-01 12:11:00 UTC  
> Url: https://github.com/boostorg/optional/issues/97#issuecomment-932174376  

Heh, okay sure, apologies for the absolute statement. From my (superficial) understanding it seems like there are two separate issues at hand:  
1. Clang CUDA not using the available intrinsics,  
2. Incomplete types being used within the fallback implementation of `BOOST_IS_BASE_OF`.  
  
I am however confused as to when the branch `#if defined(BOOST_CLANG) && defined(__has_feature) && !defined(__CUDACC__)` is NOT being taken (aside from the bug at hand), i.e., when the fallback mechanism was supposed to kick in. I tried compiling the above example with NVCC + Clang as a host compiler (`nvcc -ccbin clang++-10`), and it seems that at least during the host compilation phase, ` __CUDACC__` is not set, leading to the intrinsics being used.  
  
What I can say however is that replacing the check with `!defined(__NVCC__)`  as suggested by me above is in fact not the solution, because this then *does* trigger the fallback for NVCC + Clang as host, causing the incomplete type error.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-10-03 19:03:05 UTC  
> Url: https://github.com/boostorg/optional/issues/97#issuecomment-933007251  

The type_traits issues are addressed here: https://github.com/boostorg/type_traits/pull/162

---

## Comment 6

> Username: akrzemi1  
> Created at: 2026-02-20 15:24:40 UTC  
> Url: https://github.com/boostorg/optional/issues/97#issuecomment-3935578657  

So, is this issue addressed by https://github.com/boostorg/type_traits/pull/162?
