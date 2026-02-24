# #119 Prevent some warnings [Closed]

> Username: bebuch  
> Created at: 2017-09-12 11:23:49 UTC  
> Updated at: 2017-12-28 16:28:15 UTC  
> Closed at: 2017-12-28 16:28:15 UTC  
> Url: https://github.com/boostorg/test/pull/119  



---

## Review 1 [Commented]

> Username: raffienficiaud  
> Created_at: 2017-12-25 15:48:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/119#pullrequestreview-85491181  

📁 include/boost/test/impl/framework.ipp

```diff
1569 |         case 1:
1570 |             seed = static_cast<unsigned>( std::rand() ^ std::time( 0 ) ); // better init using std::rand() ^ ...
1571 |+ #if __has_cpp_attribute(fallthrough)
```

> Username: raffienficiaud  
> Created_at: 2017-12-25 15:48:52 UTC  
> Url: https://github.com/boostorg/test/pull/119#discussion_r158648887  

I believe this is not supported by all compilers, especially C++03


---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2017-12-25 15:50:32 UTC  
> Url: https://github.com/boostorg/test/pull/119#issuecomment-353877069  

I think your changes are not C++03 compliant, and the `__has_cpp_attribute` is not standard (at least from what I read [here](http://en.cppreference.com/w/cpp/experimental/feature_test)).   
  
Would you like to rework your patch, or should I close the PR?

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2017-12-25 15:52:31 UTC  
> Url: https://github.com/boostorg/test/pull/119#issuecomment-353877162  

Seems to duplicate #122 , what do you think

---
