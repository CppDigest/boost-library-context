# #46 Avoid fallback routine buffer overruns [Merged]

> Username: mborland  
> Created at: 2023-05-19 13:44:25 UTC  
> Updated at: 2023-05-22 14:41:30 UTC  
> Merged at: 2023-05-22 14:41:26 UTC  
> Closed at: 2023-05-22 14:41:26 UTC  
> Url: https://github.com/boostorg/charconv/pull/46  

Closes: #45   
  
`std::strtod` does not accept a size argument so when parsing a buffer with valid contents past `last` the results would be incorrect. Locally ran the benchmarks from #44 that caused the linked issue, and they are resolved.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-05-22 11:13:44 UTC  
> Url: https://github.com/boostorg/charconv/pull/46#issuecomment-1557030549  

@pdimov I bumped up the statically allocated array size form 256 to 1024, and added handling for a dynamic array using `malloc`. I also removed all of the default initializations like you asked. Let me know if you find anything else.

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2023-05-22 13:04:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/46#pullrequestreview-1436590827  

📁 include/boost/charconv/from_chars.hpp

```diff
 104 | 
 104 |-     value = 0;
 105 |+     std::memcpy(buffer, first, static_cast<std::size_t>(last - first));
```

> Username: pdimov  
> Created_at: 2023-05-22 13:04:38 UTC  
> Url: https://github.com/boostorg/charconv/pull/46#discussion_r1200490320  

You should add a trailing zero here.


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2023-05-22 13:04:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/46#pullrequestreview-1436591366  

📁 include/boost/charconv/from_chars.hpp

```diff
 147 |+     if (last - first < 1024)
 148 |+     {
 149 |+         char buffer[1024] {};
```

> Username: pdimov  
> Created_at: 2023-05-22 13:04:55 UTC  
> Url: https://github.com/boostorg/charconv/pull/46#discussion_r1200490757  

... which would allow you to remove the initialization here...


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2023-05-22 13:05:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/46#pullrequestreview-1436592128  

📁 include/boost/charconv/from_chars.hpp

```diff
 154 |+     // malloc is used here because it does not throw on allocation failure.
 155 |+ 
 156 |+     char* buffer = static_cast<char*>(std::malloc(last - first + 1));
```

> Username: pdimov  
> Created_at: 2023-05-22 13:05:18 UTC  
> Url: https://github.com/boostorg/charconv/pull/46#discussion_r1200491218  

... because you're not initializing `buffer` here anyway.


---

## Comment 5

> Username: pdimov  
> Created_at: 2023-05-22 13:07:47 UTC  
> Url: https://github.com/boostorg/charconv/pull/46#issuecomment-1557191269  

You should merge/rebase the `develop` branch here to pick up my new test.

---

## Comment 6

> Username: mborland  
> Created_at: 2023-05-22 13:19:16 UTC  
> Url: https://github.com/boostorg/charconv/pull/46#issuecomment-1557210347  

> You should merge/rebase the `develop` branch here to pick up my new test.  
  
The force push was a rebase onto develop so I have it, and there are no new failures.

---
