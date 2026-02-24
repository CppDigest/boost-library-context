# #298 - Strange errors deep in Boost.core libraries on old Xcode (7-10) platforms [Closed]

> Username: jeremy-murphy  
> Created at: 2022-07-12 06:57:21 UTC  
> Updated at: 2022-07-15 01:01:13 UTC  
> Closed at: 2022-07-15 01:01:13 UTC  
> Url: https://github.com/boostorg/graph/issues/298  

I accidentally overlooked these errors when I merged the fix for Stoer-Wagner, and I'm not sure whether it's easy to fix or not.  
I'd rather keep the fix for the algorithm than support these old platforms, but the broader user-base may not agree.  
  
See the failing builds here: https://drone.cpp.al/boostorg/graph/98/100/2  
  
@sebrockm are you able to look at this? Presumably the fix you wrote has triggered it, so you're probably best suited to address it.

---

## Comment 1

> Username: sebrockm  
> Created at: 2022-07-12 07:21:46 UTC  
> Url: https://github.com/boostorg/graph/issues/298#issuecomment-1181409108  

@jeremy-murphy Very strange indeed. I hope I can have a look soon.  
I literally only copy pasted the old code and applied clang-format.  
Note, that my PR has also some failed builds on Linux: https://drone.cpp.al/boostorg/graph/97  
But now that it is merged, on master those Linux builds are passing and only some OSX builds are failing? 🤔   
Any idea how that can happen? Is CI on PRs slightly different from CI on master?

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2022-07-12 07:32:55 UTC  
> Url: https://github.com/boostorg/graph/issues/298#issuecomment-1181418894  

Yes it is strange. Really, people that use those old platforms should be helping maintain them.  
The Linux builds on your PR just had some transient network error that prevented them from building.

---

## Comment 3

> Username: pdimov  
> Created at: 2022-07-12 09:42:39 UTC  
> Url: https://github.com/boostorg/graph/issues/298#issuecomment-1181549028  

```  
../../../boost/type_traits/detail/has_binary_operator.hpp:68:96: error: no template named 'declval' in namespace 'std'; did you mean simply 'declval'?  
      struct BOOST_JOIN(BOOST_TT_TRAIT_NAME, _dc_imp)<T, U, typename boost::make_void<decltype(std::declval<typename add_reference<T>::type>() BOOST_TT_TRAIT_OP std::declval<typename add_reference<U>::type>())>::type>  
                                                                                               ^~~~~  
../../../boost/type_traits/declval.hpp:40:44: note: 'declval' declared here  
    typename add_rvalue_reference<T>::type declval() BOOST_NOEXCEPT; // as unevaluated operand  
                                           ^  
```  
Looks like a straightforward bug in TypeTraits has_binary_operator: std::declval is being used without its header being included, which it shouldn't be anyway because it's C++11. The compiler suggestion of using boost::declval instead seems correct. An issue in TypeTraits should be opened about this, and someone should probably prepare a minimal test case.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-07-12 11:28:32 UTC  
> Url: https://github.com/boostorg/graph/issues/298#issuecomment-1181643723  

>Looks like a straightforward bug in TypeTraits has_binary_operator: std::declval is being used without its header being included, which it shouldn't be anyway because it's C++11. The compiler suggestion of using boost::declval instead seems correct. An issue in TypeTraits should be opened about this, and someone should probably prepare a minimal test case.  
  
Nope, that branch in the code is for C++11 and up: `std::declval` should be present, and it's header - `<utility>` *is* being included.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-07-12 11:50:55 UTC  
> Updated at: 2022-07-12 11:51:15 UTC  
> Url: https://github.com/boostorg/graph/issues/298#issuecomment-1181663833  

Should be a problem with old libc++ lacking `std::declval` in `<utility>`, then.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2022-07-12 12:04:28 UTC  
> Url: https://github.com/boostorg/graph/issues/298#issuecomment-1181676789  

I have no insight into the errors, but I note that Config is now only testing XCode-10 and up.  To be honest I would be inclined to do the same here.  I don't like "hiding" issues like that, but if anyone is using those ancient XCode versions, then they might be able to shed some light on the matter with a suitable PR.  At the same time the CI scripts should probably be updated with the latest-and-greatest compiler versions.

---

## Comment 7

> Username: jeremy-murphy  
> Created at: 2022-07-12 20:44:40 UTC  
> Url: https://github.com/boostorg/graph/issues/298#issuecomment-1182484258  

Thanks for your help, I'll close this and update the CI scripts accordingly.
