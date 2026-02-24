# #38 Fix compilation error with as_deque in C++11. [Merged]

> Username: ldionne  
> Created at: 2014-11-12 18:49:47 UTC  
> Updated at: 2014-11-13 00:52:03 UTC  
> Merged at: 2014-11-12 23:12:55 UTC  
> Closed at: 2014-11-12 23:12:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/38  

In C++11, `as_deque` did not compile for even simple use cases. I added a unit test and a fix (which is not 100% clean, see inline comments). With the patch applied, all the unit tests are passing on develop with Apple's Clang and -std=c++1y.

---

## Comment 1

> Username: ldionne  
> Created_at: 2014-11-12 18:54:53 UTC  
> Updated_at: 2014-11-12 20:00:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/38#discussion_r20240804  

This constructor hides the `Sequence const&` constructor below in the file. When trying to construct a deque from a sequence, this will fail because we'll take the sequence itself as being an element of the deque. So basically, I added a `enable_if` which disables this constructor if the `Head_` type can't be converted into the first type in the deque. A more complete solution would be to check whether `Head_` and all of `Tail_...` can be converted to the corresponding deque type.

---

## Comment 2

> Username: ldionne  
> Created_at: 2014-11-12 18:59:07 UTC  
> Updated_at: 2014-11-12 20:00:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/38#discussion_r20241179  

Actually, I think this fix might be just the correct one; if more than one argument is passed to the constructor, the `Sequence const&` constructor below will be ignored anyway. So checking for convertibility of the `Head_` type only should be enough. Thoughts?

---

## Comment 3

> Username: ldionne  
> Created_at: 2014-11-12 19:18:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/38#issuecomment-62776325  

Wait before merging this! This does not fix _all_ the problems; I'm investigating a problem with nested `deque`s.

---

## Comment 4

> Username: ldionne  
> Created_at: 2014-11-12 20:04:32 UTC  
> Url: https://github.com/boostorg/fusion/pull/38#discussion_r20246583  

When the deque is a `deque<deque<...>>`, these constructors will cause problems because they will mistake the first and only element for a copy construction. Actually, I think the right fix here would be to check whether all of `Head_` and `Tail_` are convertible to their corresponding element, but that's a bit more complicated and it adds compile-time overhead. Thoughts about the right fix?

---

## Comment 5

> Username: ldionne  
> Created_at: 2014-11-12 20:08:13 UTC  
> Url: https://github.com/boostorg/fusion/pull/38#issuecomment-62784587  

Ok, so the pull request passes the basic tests we have so far, but clearly there are still problems because stuff like  
  
```  
deque<> xs;  
BOOST_TEST(  
    as_deque(push_front(xs, make_vector(1, '2', 3.3f))) ==  
    make_deque(make_vector(1, '2', 3.3f))  
);  
```  
  
still fails to compile. I'm out of time to resolve everything though.

---

## Comment 6

> Username: djowel  
> Created_at: 2014-11-12 23:04:41 UTC  
> Updated_at: 2014-11-12 23:05:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/38#discussion_r20259674  

Makes sense. Yes, that will add too much compile time.

---

## Comment 7

> Username: djowel  
> Created_at: 2014-11-12 23:12:48 UTC  
> Url: https://github.com/boostorg/fusion/pull/38#issuecomment-62813056  

OK, just leave the failing test there so that I or someone else can work on fixing it. Thanks, Louis!

---

## Comment 8

> Username: ldionne  
> Created_at: 2014-11-12 23:19:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/38#issuecomment-62813861  

Glad this is merged. However, note that the failing test was not in the PR.

---

## Comment 9

> Username: djowel  
> Created_at: 2014-11-13 00:52:03 UTC  
> Url: https://github.com/boostorg/fusion/pull/38#issuecomment-62823223  

Oh OKI'll add it. It's better to have something nagging us to be fixed, than be forgotten. Thanks!

---
