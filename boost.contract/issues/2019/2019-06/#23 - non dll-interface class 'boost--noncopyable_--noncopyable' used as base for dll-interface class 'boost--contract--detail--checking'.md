# #23 - non dll-interface class 'boost::noncopyable_::noncopyable' used as base for dll-interface class 'boost::contract::detail::checking' [Closed]

> Username: jeking3  
> Created at: 2019-06-04 13:09:34 UTC  
> Updated at: 2019-06-04 18:25:54 UTC  
> Closed at: 2019-06-04 18:25:54 UTC  
> Url: https://github.com/boostorg/contract/issues/23  

This is from a teeks msvc 14.2 build that boost-ci doesn't have in it yet.  Likely all other windows builds are issuing the same warning however.  
  
https://www.boost.org/development/tests/master/developer/output/teeks99-07-v16ml-64onAMD64-boost-bin-v2-libs-contract-test-specify-auto_error-test-msvc-14-2-dbg-adrs-mdl-64-thrd-mlt.html  
  
```  
D:\teeks99-07\run\boost_root\boost/contract/detail/checking.hpp(26): warning C4275: non dll-interface class 'boost::noncopyable_::noncopyable' used as base for dll-interface class 'boost::contract::detail::checking'  
D:\teeks99-07\run\boost_root\boost/core/noncopyable.hpp(38): note: see declaration of 'boost::noncopyable_::noncopyable'  
D:\teeks99-07\run\boost_root\boost/contract/detail/checking.hpp(24): note: see declaration of 'boost::contract::detail::checking'  
```

---

## Comment 1

> Username: lcaminiti  
> Created at: 2019-06-04 16:35:37 UTC  
> Updated at: 2019-06-04 18:25:45 UTC  
> Url: https://github.com/boostorg/contract/issues/23#issuecomment-498748175  

I fixed quite a few warnings in develop (but I haven't merged into master yet). Looking at the test matrix for develop I don't see warning for msvc-14.2:  
https://www.boost.org/development/tests/develop/developer/contract.html  
The link above is from master. Is the warning C4275 above from develop or master?

---

## Comment 2

> Username: jeking3  
> Created at: 2019-06-04 16:54:14 UTC  
> Url: https://github.com/boostorg/contract/issues/23#issuecomment-498755090  

Looks like it was from master.

---

## Comment 3

> Username: lcaminiti  
> Created at: 2019-06-04 18:25:54 UTC  
> Url: https://github.com/boostorg/contract/issues/23#issuecomment-498787804  

This should be fixed in develop.
