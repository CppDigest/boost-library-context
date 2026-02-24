# #268 Set parse_options from fuzzdata and invoke to_string() [Closed]

> Username: pauldreik  
> Created at: 2020-09-04 16:25:25 UTC  
> Updated at: 2020-09-04 20:23:59 UTC  
> Closed at: 2020-09-04 17:38:27 UTC  
> Url: https://github.com/boostorg/json/pull/268  

This should cover more of the code. I have run this locally, no crashes found.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-04 16:35:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/268#pullrequestreview-482803448  

📁 fuzzing/fuzz_parser.cpp

```diff
  50 |+     popt.allow_trailing_commas=!!(data[0]&0x2);
  51 |+     popt.allow_invalid_utf8=!!(data[0]&0x4);
  52 |+     popt.max_depth= (data[0]>>3);
```

> Username: vinniefalco  
> Created_at: 2020-09-04 16:35:00 UTC  
> Updated_at: 2020-09-04 16:35:01 UTC  
> Url: https://github.com/boostorg/json/pull/268#discussion_r483734659  

hey that's really cool!!!

> Username: pauldreik  
> Created_at: 2020-09-04 19:56:38 UTC  
> Updated_at: 2020-09-04 19:56:39 UTC  
> Url: https://github.com/boostorg/json/pull/268#discussion_r483820132  

Yep, only cool kids do bit shifts and stuff :-)

> Username: vinniefalco  
> Created_at: 2020-09-04 19:57:34 UTC  
> Url: https://github.com/boostorg/json/pull/268#discussion_r483820493  

LOL no... I mean, the mechanism for prodding the fuzzing tool to fill out the parse options

> Username: pauldreik  
> Created_at: 2020-09-04 20:23:59 UTC  
> Url: https://github.com/boostorg/json/pull/268#discussion_r483829742  

Why are you not impressed with my coding skillz? I can do bitwise or, too!  
  
On a more serious note, yes it is awesome to fill out input data from the fuzzer stream. Have a look at the curl fuzzer, which sets flags and then cuts different parts of the input data as the (fake) server response.


---
