# #52 fix warning of "unescapted special character" [Closed]

> Username: HDembinski  
> Created at: 2019-04-19 08:50:32 UTC  
> Updated at: 2019-04-20 11:40:36 UTC  
> Closed at: 2019-04-19 10:25:02 UTC  
> Url: https://github.com/boostorg/core/pull/52  

Running b2 in the doc folder gave the warning:  
```  
Jamfile.v2:52: Unescaped special character in argument <format>pdf:<xsl:param>boost.url.prefix=http://www.boost.org/doc/libs/release/libs/core/doc/html  
```  
This patch fixes the warning.

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-04-19 10:19:11 UTC  
> Url: https://github.com/boostorg/core/pull/52#issuecomment-484841509  

I think, it's better to envelop the URL in quotes.

---

## Comment 2

> Username: Lastique  
> Created_at: 2019-04-19 10:25:02 UTC  
> Url: https://github.com/boostorg/core/pull/52#issuecomment-484843149  

Done in d9b524d.

---
