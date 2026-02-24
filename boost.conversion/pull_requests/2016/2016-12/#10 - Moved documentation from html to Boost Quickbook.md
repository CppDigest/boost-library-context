# #10 Moved documentation from html to Boost Quickbook. [Merged]

> Username: very-cool-name  
> Created at: 2016-12-21 20:17:32 UTC  
> Updated at: 2016-12-26 19:31:33 UTC  
> Merged at: 2016-12-26 19:30:40 UTC  
> Closed at: 2016-12-26 19:30:40 UTC  
> Url: https://github.com/boostorg/conversion/pull/10  



---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2016-12-23 19:19:29 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/conversion/pull/10#pullrequestreview-14331797  

Looks good, here are some remarks:

📁 index.html

```diff
   1 |- <html>
```

> Username: apolukhin  
> Created_at: 2016-12-23 19:13:24 UTC  
> Updated_at: 2016-12-25 20:00:50 UTC  
> Url: https://github.com/boostorg/conversion/pull/10#discussion_r93795044  

index.html is still required, but it must contain only a redirect. Here's an example: https://github.com/apolukhin/type_index/blob/develop/index.html


📁 doc/Jamfile.v2

```diff
  33 |+ 
  34 |+ ###############################################################################
  35 |+ alias boostdoc ;
```

> Username: apolukhin  
> Created_at: 2016-12-23 19:17:50 UTC  
> Updated_at: 2016-12-25 20:00:50 UTC  
> Url: https://github.com/boostorg/conversion/pull/10#discussion_r93795393  

I'm not sure about those lines. Take a look at the https://github.com/boostorg/lexical_cast/blob/develop/doc/Jamfile.v2 and https://github.com/apolukhin/type_index/blob/develop/doc/Jamfile.v2  
  
Looks like you must have the `boostbook standalone` line and different `alias boostdoc`.


---

## Review 2 [Changes requested]

> Username: apolukhin  
> Created_at: 2016-12-25 13:25:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/conversion/pull/10#pullrequestreview-14364162  

Final fix:

📁 index.html

```diff
  12 |+ <meta charset="utf-8">
  13 |+ <meta http-equiv="refresh" content="0; url=../../doc/html/conversion.html">
  14 |+ <title>Boost.TypeIndex</title>
```

> Username: apolukhin  
> Created_at: 2016-12-25 13:25:19 UTC  
> Updated_at: 2016-12-25 20:00:50 UTC  
> Url: https://github.com/boostorg/conversion/pull/10#discussion_r93829006  

Change to `Boost.Conversion`


---

## Comment 3

> Username: apolukhin  
> Created_at: 2016-12-26 19:31:32 UTC  
> Url: https://github.com/boostorg/conversion/pull/10#issuecomment-269236731  

Great thanks for rewriting the docs!

---
