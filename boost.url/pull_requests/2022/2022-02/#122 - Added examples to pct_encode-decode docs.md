# #122 Added examples to pct_encode/decode docs [Closed]

> Username: vgrigoriu  
> Created at: 2022-02-11 15:37:11 UTC  
> Updated at: 2022-03-12 04:35:42 UTC  
> Closed at: 2022-03-11 23:31:55 UTC  
> Url: https://github.com/boostorg/url/pull/122  

also fixed some typos

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-11 16:13:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/122#pullrequestreview-880387814  

📁 include/boost/url/pct_encoding.hpp

```diff
  76 |+     assert(ec);
  77 |+     @endcode
  78 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-02-11 16:13:42 UTC  
> Url: https://github.com/boostorg/url/pull/122#discussion_r804799262  

In implementation I would use `auto` but in example code I prefer to spell out the variable. And I also use additional spaces inside parenthesis because it renders better in docs. When there are multiple examples I provide a short descriptive sentence. How about:  
```  
@par Example 1  
This validates and calculates the decoded length of a valid percent-encoded string.  
@code  
error_code ec;  
std::size_t decoded_size = validate_pct_encoding( "Program%20Files", ec, pchars );  
assert( ! ec.failed() );  
assert( decoded_size == 13 );  
@endcode  
  
@par Example 2  
This shows how validation can fail using an error code:  
@code  
error_code ec;  
std::size_t decoded_size = validate_pct_encoding( "bad%2escape", ec, pchars );  
assert( ec.failed() );  
@endcode  
```


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-11 16:15:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/122#pullrequestreview-880389688  

📁 include/boost/url/pct_encoding.hpp

```diff
 201 |+     @par Example
 202 |+     @code
 203 |+     auto result = pct_decode("Some%20text", {}, pchars);
```

> Username: vinniefalco  
> Created_at: 2022-02-11 16:15:17 UTC  
> Url: https://github.com/boostorg/url/pull/122#discussion_r804800529  

In implementation I would use `{}` but since this is an example it is better to be explicit:  
```  
string_view decoded_string = pct_decode( "Program%20Files", pct_decode_options{}, pchars );  
```


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-11 16:16:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/122#pullrequestreview-880391410  

📁 include/boost/url/pct_encoding.hpp

```diff
 539 |+     assert(strncmp("My%20stuff", dest, encoded) == 0);
 540 |+     @endcode
 541 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-02-11 16:16:43 UTC  
> Url: https://github.com/boostorg/url/pull/122#discussion_r804801862  

Same notes regarding examples should be applied to all these changes

> Username: vgrigoriu  
> Created_at: 2022-02-11 16:20:34 UTC  
> Url: https://github.com/boostorg/url/pull/122#discussion_r804805244  

Sure thing.


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-02-11 17:13:29 UTC  
> Url: https://github.com/boostorg/url/pull/122#issuecomment-1036430250  

We appreciate your contributions :) How is the library working out for you? I guess you're using the percent-encoding algorithms.

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2022-02-11 18:37:50 UTC  
> Updated_at: 2022-02-14 14:59:32 UTC  
> Url: https://github.com/boostorg/url/pull/122#issuecomment-1036505437  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/122?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#122](https://codecov.io/gh/CPPAlliance/url/pull/122?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (0e4aca0) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/8911d05c35123f978e90321fea863385b63525d4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (8911d05) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/122/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/122?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #122   +/-   ##  
========================================  
  Coverage    96.22%   96.22%             
========================================  
  Files          104      104             
  Lines         5486     5486             
========================================  
  Hits          5279     5279             
  Misses         207      207             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/122?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/122?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [8911d05...0e4aca0](https://codecov.io/gh/CPPAlliance/url/pull/122?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 6

> Username: vgrigoriu  
> Created_at: 2022-02-14 11:09:44 UTC  
> Url: https://github.com/boostorg/url/pull/122#issuecomment-1038955038  

I made the changes you requested.  
  
The library is great, we're using the percent-encoding functions but also url parsing. It was just very confusing in the beginning because it took me a while to find examples of how to call the functions ("What am I supposed to send as the `cs` parameter?") plus we were using the beta-1 release and the API changed and I didn't understand why the order of parameters didn't match :-)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-02-22 06:29:53 UTC  
> Url: https://github.com/boostorg/url/pull/122#issuecomment-1047468163  

@alandefreitas is this ready to go?

---

## Review 8 [Approved]

> Username: alandefreitas  
> Created_at: 2022-02-28 19:05:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/url/pull/122#pullrequestreview-895485129  

I downloaded this locally, rebased it, and it looks great. There were lots of typos and copy/paste mistakes. There are no docca+quickbook errors or warnings, and they are rendered perfectly. The doc preview wasn't activated in this PR but here's an example:  
  
![image](https://user-images.githubusercontent.com/5369819/156042799-ff9ae6be-6e06-44e0-85d9-a19ed33100db.png)  
  
You probably want to squash the commits before merging, as usual.  
  
The examples are also very useful. They are so useful they might indicate we need an explicit section on the exposition about these percent-encoding functions.   
  
Everything about percent-encoding in the documentation is kind of implied so far, almost as if this were an implementation detail. The examples in the exposition are also nice because we can ensure they are always working.

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2022-02-28 19:27:32 UTC  
> Url: https://github.com/boostorg/url/pull/122#issuecomment-1054588977  

@alandefreitas Please squash it and submit it as a new pull request, put "close #122" in the body of the commit message

---
