# #755 Add merge examples [Closed]

> Username: vinniefalco  
> Created at: 2022-09-21 04:27:46 UTC  
> Updated at: 2022-10-26 16:13:35 UTC  
> Closed at: 2022-10-26 16:13:34 UTC  
> Url: https://github.com/boostorg/json/pull/755  

proposed for #754

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-09-21 05:39:39 UTC  
> Url: https://github.com/boostorg/json/pull/755#issuecomment-1253229889  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/755/pullrequest-condensed-e6a5013.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/755/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/755/pullrequest.html)

---

## Review 2 [Commented]

> Username: grisumbras  
> Created_at: 2022-09-21 09:33:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/755#pullrequestreview-1115098312  

📁 example/merge.cpp

```diff
  55 |+     }
  56 |+     return dest;
  57 |+ }
```

> Username: grisumbras  
> Created_at: 2022-09-21 09:33:48 UTC  
> Url: https://github.com/boostorg/json/pull/755#discussion_r976285515  

This should be almost equivalent to your code:  
```c++  
object&  
merge_objects(  
    object& dest,  
    object src)  
{  
    dest.insert(  
        std::make_move_iterator(src.begin()),  
        std::make_move_iterator(src.end()));  
    return dest;  
}  
```  
And it's basically one library function (with a little help of the standard library). `merge_copy` is the same function, only the caller capies rather than moves `src`.


---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-09-21 12:37:14 UTC  
> Url: https://github.com/boostorg/json/pull/755#issuecomment-1253648830  

NB: I'm not suggesting this be merged, its up to you to decide what to do with it (which could be, to do nothing)

---
