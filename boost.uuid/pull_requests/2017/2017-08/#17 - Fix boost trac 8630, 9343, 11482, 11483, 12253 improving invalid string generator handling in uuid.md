# #17 Fix boost trac 8630, 9343, 11482, 11483, 12253 improving invalid string generator handling in uuid [Merged]

> Username: jeking3  
> Created at: 2017-08-03 16:12:20 UTC  
> Updated at: 2017-10-18 19:37:43 UTC  
> Merged at: 2017-08-09 22:35:46 UTC  
> Closed at: 2017-08-09 22:35:46 UTC  
> Url: https://github.com/boostorg/uuid/pull/17  

For example, "12b672ea-388d-47da-9c26-bec5bc8a06cb-foo" was not considered invalid before, but it is now.

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-08-03 17:46:57 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#issuecomment-320041245  

I would like to see tests for all 4 forms when generating from a string and there is unexpected extra data at the end of the string. Your test is good for 1 of the 4 different forms.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-08-04 12:22:42 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#issuecomment-320235213  

@eldiener I updated the pull request with the additional tests.  If I can be of any assistance in grooming the boost trac backlog, let me know.

---

## Review 3 [Commented]

> Username: eldiener  
> Created_at: 2017-08-09 04:44:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/17#pullrequestreview-55132809  

📁 include/boost/uuid/string_generator.hpp

```diff
  85 |-                 if (i == 6 || i == 8 || i == 10) {
  84 |+             // if there are dashes, they must be in every slot
  85 |+             if (i == 6 || i == 8 || i == 10) {
```

> Username: eldiener  
> Created_at: 2017-08-09 04:44:43 UTC  
> Updated_at: 2017-08-09 19:38:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#discussion_r132089546  

Can this not be:  
`else if (i == 6 || i == 8 || i == 10) {`

> Username: jeking3  
> Created_at: 2017-08-09 16:33:06 UTC  
> Updated_at: 2017-08-09 19:38:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#discussion_r132236564  

It can, I will push a change for it.


---

## Review 4 [Commented]

> Username: eldiener  
> Created_at: 2017-08-09 04:50:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/17#pullrequestreview-55133277  

📁 include/boost/uuid/string_generator.hpp

```diff
 133 |         static char const*const digits_end = digits_begin + 22;
```

> Username: eldiener  
> Created_at: 2017-08-09 04:50:18 UTC  
> Updated_at: 2017-08-09 19:38:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#discussion_r132090004  

This isn't your code but can't be take the sizeof(digits_begin) and use that instead of hardcoding 22 and further on down hardcoding '> 21' ? Hardcoding numbers is generally poor c++ if there is a better way to get the hardcoded value from the compiler.

> Username: jeking3  
> Created_at: 2017-08-09 16:32:57 UTC  
> Updated_at: 2017-08-09 19:38:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#discussion_r132236526  

sizeof(digits_begin) if changed to ``static wchar_t const digits_begin[] = ...`` is 46, confirmed by a BOOST_STATIC_ASSERT, so in this case I think the hardcoded values is appropriate.  Considering the enhanced testing, any changes here will be caught.

> Username: eldiener  
> Created_at: 2017-08-09 18:37:47 UTC  
> Updated_at: 2017-08-09 19:38:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#discussion_r132270529  

In the get_value(char c) case it should be:  
`sizeof(digits_begin)/sizeof(char)`  
and in the get_value(wchar_t c) case it should be:  
`sizeof(digits_begin)/sizeof(wchar_t)`  
  
so that hardcoded numbers are not used.

> Username: jeking3  
> Created_at: 2017-08-09 19:28:19 UTC  
> Updated_at: 2017-08-09 19:38:47 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#discussion_r132282594  

Okay, we'll have to subtract one, as the digits_end needs to point at the trailing NULL, but I will make this change.


---

## Comment 5

> Username: eldiener  
> Created_at: 2017-08-09 04:53:41 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#issuecomment-321152374  

The code works. Please see my comments for some possible individual changes.   
  
I think the docs should be updated to both explain all the valid string formats uuid accepts and specifies that uuid throws a particular exception if the string is not a valid format or contains invalid hex digits.

---

## Comment 6

> Username: jeking3  
> Created_at: 2017-08-09 16:17:00 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#issuecomment-321305307  

I wonder if a boost::regex would be sufficient not only to describe the valid format but also enforce and to parse it, in the future.  That would force a dependency that folks may not want however, and it may not be documentation-friendly.

---

## Comment 7

> Username: jeking3  
> Created_at: 2017-08-09 18:02:04 UTC  
> Url: https://github.com/boostorg/uuid/pull/17#issuecomment-321334527  

I pushed an update that addresses all concerns thus far.

---
