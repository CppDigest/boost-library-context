# #45 fix clang "parameter not found" warnings [Merged]

> Username: samuela  
> Created at: 2018-03-21 00:50:20 UTC  
> Updated at: 2018-03-21 02:07:58 UTC  
> Merged at: 2018-03-21 02:05:04 UTC  
> Closed at: 2018-03-21 02:05:04 UTC  
> Url: https://github.com/boostorg/algorithm/pull/45  

Pretty simple.

---

## Review 1 [Commented]

> Username: mclow  
> Created_at: 2018-03-21 01:40:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/45#pullrequestreview-105590528  

📁 include/boost/algorithm/string/formatter.hpp

```diff
  85 |             BOOST_STRING_TYPENAME range_value<RangeT>::type>
  86 |-         empty_formatter(const RangeT&)
  86 |+         empty_formatter(const RangeT& Input)
```

> Username: mclow  
> Created_at: 2018-03-21 01:40:58 UTC  
> Updated_at: 2018-03-21 02:02:08 UTC  
> Url: https://github.com/boostorg/algorithm/pull/45#discussion_r175969808  

I don't think the parameter is used; so naming it here would probably generate other warnings; something like "input not used"

> Username: samuela  
> Created_at: 2018-03-21 01:46:54 UTC  
> Updated_at: 2018-03-21 02:02:08 UTC  
> Url: https://github.com/boostorg/algorithm/pull/45#discussion_r175970343  

Mmm, true. I didn't observe such a warning but I'm sure they would come up. The docstring "parameter not found" doesn't like unnamed arguments as far as I can tell.  
  
Which warning is preferred? Should I revert this one?

> Username: mclow  
> Created_at: 2018-03-21 01:50:49 UTC  
> Updated_at: 2018-03-21 02:02:08 UTC  
> Url: https://github.com/boostorg/algorithm/pull/45#discussion_r175970666  

I'd rather have a docstring warning than a compiler warning.

> Username: mclow  
> Created_at: 2018-03-21 01:51:28 UTC  
> Updated_at: 2018-03-21 02:02:08 UTC  
> Url: https://github.com/boostorg/algorithm/pull/45#discussion_r175970704  

The other way to deal with it is to add `(void) Input;` to the body of the function.

> Username: samuela  
> Created_at: 2018-03-21 02:02:34 UTC  
> Url: https://github.com/boostorg/algorithm/pull/45#discussion_r175971558  

Ok, reverted in c34713b!

> Username: mclow  
> Created_at: 2018-03-21 02:05:14 UTC  
> Url: https://github.com/boostorg/algorithm/pull/45#discussion_r175971757  

thanks!


---
