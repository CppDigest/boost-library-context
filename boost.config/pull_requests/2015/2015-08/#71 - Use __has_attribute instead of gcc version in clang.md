# #71 Use __has_attribute instead of gcc version in clang [Merged]

> Username: alkino  
> Created at: 2015-08-27 11:34:56 UTC  
> Updated at: 2015-09-10 15:22:06 UTC  
> Merged at: 2015-09-01 16:52:37 UTC  
> Closed at: 2015-09-01 16:52:37 UTC  
> Url: https://github.com/boostorg/config/pull/71  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-08-27 12:08:02 UTC  
> Url: https://github.com/boostorg/config/pull/71#issuecomment-135402579  

The   
  
#ifndef __has_cpp_attribute  
  
branch block should be:  
  
#ifndef __has_attribute  
  
?

---

## Comment 2

> Username: alkino  
> Created_at: 2015-08-27 12:09:53 UTC  
> Url: https://github.com/boostorg/config/pull/71#issuecomment-135403311  

Hum sorry for that. Fixed now.

---

## Comment 3

> Username: morinmorin  
> Created_at: 2015-09-03 09:39:20 UTC  
> Updated_at: 2015-09-10 15:22:06 UTC  
> Url: https://github.com/boostorg/config/pull/71#issuecomment-137392342  

The proposed detection code suffers from macro interference on not-so-recent versions of Clang (up to ver. 3.2): with `#define unused foo`, `__has_attribute(unused)` gets expanded to `__has_attribute(foo)`. The old detection code looks a bit awkward, but it's free from macro interference.   
  
Or rather, as Clang has supported the `unused` attribute since its first release, we can completely remove the detection code. I'll make a PR for this later.  
  
I'll also make a PR that adds double underscores in  
  
```  
#  define BOOST_ATTRIBUTE_UNUSED __attribute__((unused))  
```  
  
P.S.  
Here is history of `__has_attribute` on Clang.  
- (Clang 2.8 introduced `__GNUC__` as 4.)  
- Clang 2.9 introduced `__has_attribute`.  
- Clang 3.1 started to accept names with double underscores (`__xxxx__`) in `__has_attribute`.  
- Clang 3.3 started to avoid macro expansion in `__has_attribute`.

---

## Comment 4

> Username: alkino  
> Created_at: 2015-09-03 09:42:17 UTC  
> Url: https://github.com/boostorg/config/pull/71#issuecomment-137392893  

Thanks for pointing it

---

## Comment 5

> Username: morinmorin  
> Created_at: 2015-09-03 09:56:56 UTC  
> Url: https://github.com/boostorg/config/pull/71#issuecomment-137396141  

Thanks for your contribution and heads-up too, Nicolas!

---
