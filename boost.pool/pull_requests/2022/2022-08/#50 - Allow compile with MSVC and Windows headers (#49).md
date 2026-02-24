# #50 Allow compile with MSVC and Windows headers (#49) [Closed]

> Username: joelpelaez  
> Created at: 2022-08-26 02:26:47 UTC  
> Updated at: 2024-09-26 05:37:39 UTC  
> Closed at: 2024-09-26 05:37:33 UTC  
> Url: https://github.com/boostorg/pool/pull/50  

When the boost::pool is instantiated and the code is compiled with  
MSVC compiler, it can fail if Windows specific headers are included,  
adding the max and min macros.  
  
Because some part of the header file already has preprocessor guards,  
this change add same solution to call to std::numeric_limits<>::max()  
to avoid any issues.  
  
On MSVC using C++20 standard mode, the header file also fires the same  
issue, only with include it on code file, without require object  
instantiation.

---

## Review 1 [Commented]

> Username: mclow  
> Created_at: 2022-08-26 03:48:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pool/pull/50#pullrequestreview-1086363863  

📁 include/boost/pool/pool.hpp

```diff
 362 |       size_type POD_size = integer::static_lcm<sizeof(size_type), sizeof(void *)>::value + sizeof(size_type);
 363 |-       return (std::numeric_limits<size_type>::max() - POD_size) / alloc_size();
 363 |+       return ((std::numeric_limits<size_type>::max)() - POD_size) / alloc_size();
```

> Username: mclow  
> Created_at: 2022-08-26 03:48:35 UTC  
> Url: https://github.com/boostorg/pool/pull/50#discussion_r955624830  

Is this a better workaround than the use of `BOOST_PREVENT_MACRO_SUBSTITUTION` elsewhere in this file?

> Username: mclow  
> Created_at: 2022-08-26 03:49:54 UTC  
> Url: https://github.com/boostorg/pool/pull/50#discussion_r955625274  

For example on line 412

> Username: joelpelaez  
> Created_at: 2022-08-26 04:00:49 UTC  
> Updated_at: 2022-08-26 04:00:50 UTC  
> Url: https://github.com/boostorg/pool/pull/50#discussion_r955628964  

I used the same as line 351  
  
https://github.com/boostorg/pool/blob/e0db4278cafecd820e3575877f17afcbd441110c/include/boost/pool/pool.hpp#L351

> Username: joelpelaez  
> Created_at: 2022-08-26 04:07:28 UTC  
> Url: https://github.com/boostorg/pool/pull/50#discussion_r955631325  

I think in use `BOOST_PREVENT_MACRO_SUBSTITUTION` but it extends the line length and reduce readability.

> Username: mclow  
> Created_at: 2022-09-05 03:23:08 UTC  
> Updated_at: 2022-09-05 03:23:34 UTC  
> Url: https://github.com/boostorg/pool/pull/50#discussion_r962447097  

I disagree.  
The bare parentheses fix the problem, and they're small.  
But they give no clue as to why they are there - and that's my concern.  
  
In the future, someone will come along and remove the "redundant" parens.  
Hopefully, when that happens, this whole "windows header" thing will be a bad memory.

> Username: johanneskopf  
> Created_at: 2023-09-26 13:50:03 UTC  
> Updated_at: 2023-09-26 14:03:11 UTC  
> Url: https://github.com/boostorg/pool/pull/50#discussion_r1337246653  

Would be great if this could be merged. As when compiling a project with boost 1.82 or 1.83 I run into the following warning many times: `C4003: not enough arguments for function-like macro invocation 'max'` and this seems to be the culprit.  
  
I also found an issue in another boost lib that addresses the same problem this way:  
https://github.com/boostorg/beast/pull/1981/commits/24a1196e4c36ac34b0072876075f3dba9bec1d35  
Issue it fixed: https://github.com/boostorg/beast/issues/1980.  
Would it help to add a comment to explicitly state why those parentheses are there?


---
