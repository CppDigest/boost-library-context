# #85 Fix more allocator traits [Merged]

> Username: jefftrull  
> Created at: 2020-04-08 19:00:24 UTC  
> Updated at: 2020-04-14 22:30:07 UTC  
> Merged at: 2020-04-10 15:23:11 UTC  
> Closed at: 2020-04-10 15:23:11 UTC  
> Url: https://github.com/boostorg/wave/pull/85  

If merged this will resolve #84. Thanks to @qis for the assistance.

---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2020-04-09 13:54:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/85#pullrequestreview-390814002  

@jefftrull It would be less maintenance and less `#if !defined(BOOST_NO_CXX11_ALLOCATOR)`  everywhere if you just defined in one place a `boost::wave::util::allocator_traits` and use that everywhere. That one class template would check `BOOST_NO_CXX11_ALLOCATOR` and inside use `std::allocator_traits` if available. All other code in Wave would just be expressed in terms of `boost::wave::util::allocator_traits`.

---

## Comment 2

> Username: glenfe  
> Created_at: 2020-04-09 13:57:14 UTC  
> Url: https://github.com/boostorg/wave/pull/85#issuecomment-611542223  

@jefftrull Something like:  
  
```cpp  
namespace boost {  
namespace wave {  
namespace util {  
  
#if !defined(BOOST_NO_CXX11_ALLOCATOR)  
using std::allocator_traits;  
#else  
template<class A>  
struct allocator_traits {  
    typedef typename A::value_type value_type;  
    typedef typename A::pointer pointer;  
    // Fill in the rest as needed.  
};  
#endif  
  
} // util  
} // wave  
} // boost  
```

---

## Comment 3

> Username: jefftrull  
> Created_at: 2020-04-09 14:50:21 UTC  
> Url: https://github.com/boostorg/wave/pull/85#issuecomment-611570255  

Looks like this problem has been solved by a few other libraries and appears to be in the public interface of Container - is it worth simply using that one? https://www.boost.org/doc/libs/1_72_0/doc/html/boost/container/allocator_traits.html

---

## Comment 4

> Username: glenfe  
> Created_at: 2020-04-09 15:05:31 UTC  
> Url: https://github.com/boostorg/wave/pull/85#issuecomment-611578724  

@jefftrull That would be fine too if there are no objections to Boost.Wave taking a dependency on Boost.Container (which it doesn't right now, I believe).

---

## Comment 5

> Username: Lastique  
> Created_at: 2020-04-09 15:18:39 UTC  
> Url: https://github.com/boostorg/wave/pull/85#issuecomment-611585952  

@glenfe @igaztanaga Another option would be to move `allocator_traits` from Boost.Container to Boost.Core. There are other places where we need `allocator_traits` but not the rest of Boost.Container.

---

## Comment 6

> Username: jefftrull  
> Created_at: 2020-04-09 15:40:42 UTC  
> Url: https://github.com/boostorg/wave/pull/85#issuecomment-611597723  

Upon reflection I don't think it makes sense to add a dependency for the benefit of this one string class within one library. Unless `allocator_traits` goes into Core I'll stick with a minimal local version.

---

## Review 7 [Approved]

> Username: glenfe  
> Created_at: 2020-04-09 16:04:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/85#pullrequestreview-390928986  

Looks good. You could put it in a `<boost/wave/util/allocator_traits.hpp>` header so that it could later be used in other parts of Wave which don't need to include `flex_string.hpp`

---

## Comment 8

> Username: jefftrull  
> Created_at: 2020-04-10 15:21:56 UTC  
> Url: https://github.com/boostorg/wave/pull/85#issuecomment-612076114  

The Travis job has been hung for > 24h so I'm just going to merge it.

---

## Comment 9

> Username: glenfe  
> Created_at: 2020-04-14 20:54:21 UTC  
> Url: https://github.com/boostorg/wave/pull/85#issuecomment-613676326  

@Lastique https://github.com/boostorg/core/commit/c31e23b36294bc3958b04deef62123abce0204b9

---

## Comment 10

> Username: Lastique  
> Created_at: 2020-04-14 22:17:35 UTC  
> Url: https://github.com/boostorg/wave/pull/85#issuecomment-613708892  

@glenfe Thank you Glen.

---

## Comment 11

> Username: jefftrull  
> Created_at: 2020-04-14 22:30:07 UTC  
> Url: https://github.com/boostorg/wave/pull/85#issuecomment-613712774  

Will refactor to use that in 1.74.

---
