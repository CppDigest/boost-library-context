# #55 Add basic CMake build system [Closed]

> Username: samcday  
> Created at: 2019-04-28 15:47:26 UTC  
> Updated at: 2019-06-07 06:06:31 UTC  
> Closed at: 2019-06-07 06:06:31 UTC  
> Url: https://github.com/boostorg/random/pull/55  

Just directly ripping off the work I've noticed @Mike-Devel doing across a bunch of Boost modules.  
  
I determined the list of dependencies by grepping for `#include` directives. I don't know this library very well, so let me know if any of the dependencies don't make sense.  
  
This PR depends on:  
  
 * [dynamic_bitset](https://github.com/boostorg/dynamic_bitset/pull/44)  
 * [multiprecision](https://github.com/boostorg/multiprecision/pull/131) (cyclic!)  
 * [range](https://github.com/boostorg/range/pull/92)

---

## Comment 1

> Username: swatanabe  
> Created_at: 2019-05-02 17:57:42 UTC  
> Url: https://github.com/boostorg/random/pull/55#issuecomment-488770505  

AMDG  
  
On 4/28/19 9:47 AM, Sam wrote:  
> Just directly ripping off the work I've noticed @Mike-Devel doing across a bunch of Boost modules.  
>   
> I determined the list of dependencies by grepping for `#include` directives. I don't know this library very well, so let me know if any of the dependencies don't make sense.  
>   
  
I have no interest in maintaining CMake build files  
myself.  Therefore, if you are not confident that  
this is correct, I'm not going to accept it.  
  
In Christ,  
Steven Watanabe

---

## Review 2 [Changes requested]

> Username: Mike-Devel  
> Created_at: 2019-05-13 13:15:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/random/pull/55#pullrequestreview-236639880  

📁 CMakeLists.txt

```diff
  22 |+         Boost::config
  23 |+         Boost::core
  24 |+         Boost::detail
```

> Username: Mike-Devel  
> Created_at: 2019-05-13 12:19:22 UTC  
> Updated_at: 2019-05-13 13:15:08 UTC  
> Url: https://github.com/boostorg/random/pull/55#discussion_r283317479  

Boost.Random does not depend on Boost.Detail.   
  
    #include <boost/detail/workaround.hpp>   
  
lives in Boost.Config


---
