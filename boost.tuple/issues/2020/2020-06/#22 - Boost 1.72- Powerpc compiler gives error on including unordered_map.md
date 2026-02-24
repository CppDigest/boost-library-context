# #22 - Boost 1.72: Powerpc compiler gives error on including unordered_map [Open]

> Username: sbrahul  
> Created at: 2020-06-10 10:47:05 UTC  
> Updated at: 2025-05-16 15:08:32 UTC  
> Url: https://github.com/boostorg/tuple/issues/22  

When including unordered_map.hpp file, the powerpc compiler (gcc 4.7.2) gives an error inside the tuple_basic.hpp.  
I am actually upgrading boost on the project from 1.42 to 1.72. I dont see any errors from the 1.42 version.  
  
```  
In file included from /boost/1_72_0/include/boost/tuple/tuple.hpp:28:0,  
                 from /boost/1_72_0/include/boost/unordered/detail/implementation.hpp:31,  
                 from /boost/1_72_0/include/boost/unordered/detail/map.hpp:6,  
                 from /boost/1_72_0/include/boost/unordered/unordered_map.hpp:21,  
                 from /boost/1_72_0/include/boost/unordered_map.hpp:17,  
                 from build/ppc2/system/util/WeakReference.h:17,  
                 from build/ppc2/system/util/WeakReference.c++:8:  
/boost/1_72_0/include/boost/tuple/detail/tuple_basic.hpp:704:60: error: ignoring attributes on template argument 'void (boost::tuples::detail::swallow_assign::*(void (boost::tuples::detail::swallow_assign::*)()))()' [-Werror]  
/boost/1_72_0/include/boost/tuple/detail/tuple_basic.hpp:836:37: error: ignoring attributes on template argument 'void (boost::tuples::detail::swallow_assign::*(void (boost::tuples::detail::swallow_assign::*)()))()' [-Werror]  
```

---

## Comment 1

> Username: cmazakas  
> Created at: 2021-10-18 21:14:58 UTC  
> Url: https://github.com/boostorg/tuple/issues/22#issuecomment-946172019  

Hmm, it seems like you're also using `-Werror` so all warnings are being translated into errors.  
  
In the interim, are you able to remove `-Werror` or make an exception for this compiler warning?  
  
I'll investigate this issue. Do you have a minimal complete example that demonstrates your issue?

---

## Comment 2

> Username: pdimov  
> Created at: 2021-10-18 22:03:47 UTC  
> Url: https://github.com/boostorg/tuple/issues/22#issuecomment-946203377  

This seems to be an issue with Boost.Tuple and not Boost.Unordered.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-10-18 22:29:07 UTC  
> Url: https://github.com/boostorg/tuple/issues/22#issuecomment-946218597  

It concerns `boost::tuple::ignore`, but that's not used in Boost.Unordered. So the include chain seems to be a red herring.  
  
I'm transferring this issue to Boost.Tuple.

---

## Comment 4

> Username: sbrahul  
> Created at: 2021-12-24 13:20:25 UTC  
> Url: https://github.com/boostorg/tuple/issues/22#issuecomment-1000837532  

The issue occurs when I pass "-mlongcall" option to gcc. Else, I dont see the error. Not sure how the longcall option is interfering with the code.
