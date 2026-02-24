# #86 - Using function_type on a reference gives a non-obvious error message [Closed]

> Username: ldionne  
> Created at: 2016-12-12 04:34:06 UTC  
> Updated at: 2017-02-11 05:38:44 UTC  
> Closed at: 2017-02-11 05:38:43 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/86  

Instead of failing with something like `function_type can't be used with ref-qualified types`, we get a SFINAE error saying that the library is unable to determine the parameters. This is somewhat misleading, and it took me a good couple of minutes to figure this one out.

---

## Comment 1

> Username: badair  
> Created at: 2016-12-20 05:14:57 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/86#issuecomment-268156996  

Ah, thanks.  
  
Do you think the library should support reference types to function objects? We could simply remove reference at the top level, but at that point a case could be made to remove everything else as well.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-12-20 15:38:03 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/86#issuecomment-268274695  

I don't know. I don't think it should, but we should at least get  a clear error message.

---

## Comment 3

> Username: badair  
> Created at: 2017-01-31 01:39:47 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/86#issuecomment-276248411  

@ldionne Do you think the "SFINAE errors" approach is more of a hindrance than a convenience?

---

## Comment 4

> Username: ldionne  
> Created at: 2017-02-01 17:14:06 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/86#issuecomment-276718811  

I don't know, but here's what I get:  
  
```c++  
#include <boost/callable_traits.hpp>  
struct F { void operator()(int) {} };  
using XX = boost::callable_traits::function_type<F&>::type;  
  
int main() { }  
```  
  
Error:  
```  
boost/callable_traits/detail/sfinae_errors.hpp:34:9: error:  
      no type named 'type' in  
      'boost::callable_traits::error::parameters<boost::callable_traits::cannot_determine_parameters_for_this_type_>::_'  
        using sfinae_try = typename CALLABLE_TRAITS_DISJUNCTION(  
        ^~~~~  
boost/callable_traits/detail/utility.hpp:48:1: note:  
      in instantiation of template type alias 'sfinae_try' requested here  
using fail_if_invalid = sfinae_try<T,  
^  
boost/callable_traits/function_type.hpp:24:1: note:  
      in instantiation of template type alias 'fail_if_invalid' requested here  
using function_type_t = //implementation-defined  
^  
boost/callable_traits/function_type.hpp:33:18: note:  
      in instantiation of template type alias 'function_type_t' requested here  
    using type = function_type_t<T>;  
                 ^  
test.cpp:7:36: note: in instantiation of template class  
      'boost::callable_traits::function_type<F &>' requested here  
using XX = boost::callable_traits::function_type<F&>::type;  
                                   ^  
1 error generated.  
```  
  
I thought somehow function objects were not supported or something weird like that. It could be the way it's worded: "unable to determine parameters" makes it sound like it's something you COULD determine, but don't because of some defficiency in the library.

---

## Comment 5

> Username: badair  
> Created at: 2017-02-10 21:30:08 UTC  
> Updated at: 2017-02-10 21:30:22 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/86#issuecomment-279071038  

Pending CI testing, fixed via d87078752dd2769f444d1d2d531d6a500aae86d4.  
  
SFINAE error message now reads:  
  
```  
error: no type named ‘type’ in ‘struct boost::callable_traits::error::reference_error<boost::callable_traits::reference_type_not_supported_by_this_metafunction_>::_’  
```
