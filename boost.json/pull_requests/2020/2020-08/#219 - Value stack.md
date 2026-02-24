# #219 Value stack [Merged]

> Username: vinniefalco  
> Created at: 2020-08-28 02:19:07 UTC  
> Updated at: 2020-08-28 17:24:37 UTC  
> Merged at: 2020-08-28 17:10:45 UTC  
> Closed at: 2020-08-28 17:10:45 UTC  
> Url: https://github.com/boostorg/json/pull/219  

More improvements and refactoring

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-08-28 04:56:23 UTC  
> Url: https://github.com/boostorg/json/pull/219#issuecomment-682324631  

An automated preview of the documentation is available at [http://219.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://219.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-08-28 13:36:23 UTC  
> Url: https://github.com/boostorg/json/pull/219#issuecomment-682579335  

An automated preview of the documentation is available at [http://219.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://219.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 3 [Commented]

> Username: sdkrystian  
> Created_at: 2020-08-28 15:19:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/219#pullrequestreview-477811379  

📁 include/boost/json/parse_options.hpp

```diff
  34 |+         structures allowed while parsing a JSON. If
  35 |+         this limit is exceeded, the parser returns
  36 |+         an error immediately.
```

> Username: sdkrystian  
> Created_at: 2020-08-28 15:12:25 UTC  
> Updated_at: 2020-08-28 16:38:26 UTC  
> Url: https://github.com/boostorg/json/pull/219#discussion_r479369133  

I don't think we need to say "immediately".


📁 include/boost/json/parser.hpp

```diff
 155 |+ 
 156 |+         @li A caller-owned temporary buffer to use
 157 |+         first, before allocating using the memory
```

> Username: sdkrystian  
> Created_at: 2020-08-28 15:15:46 UTC  
> Updated_at: 2020-08-28 16:38:26 UTC  
> Url: https://github.com/boostorg/json/pull/219#discussion_r479371075  

Maybe:  
> A caller-owned temporary buffer to use before allocating with the memory resource [...]


---
