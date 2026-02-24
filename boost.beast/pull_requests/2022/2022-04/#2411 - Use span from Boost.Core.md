# #2411 Use span from Boost.Core [Merged]

> Username: glenfe  
> Created at: 2022-04-21 13:12:02 UTC  
> Updated at: 2022-04-23 04:28:26 UTC  
> Merged at: 2022-04-23 04:28:26 UTC  
> Closed at: 2022-04-23 04:28:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2411  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-04-21 13:38:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#issuecomment-1105219719  

![pullrequest](https://2411.beast.tracing.cppalliance.org/pullrequest-c277929a.png)  
Timeline tracing charts: [https://2411.beast.tracing.cppalliance.org/index.html](https://2411.beast.tracing.cppalliance.org/index.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-04-21 14:08:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2411#pullrequestreview-948684899  

📁 include/boost/beast/core/span.hpp

```diff
  17 |- #include <string>
  18 |- #include <type_traits>
  13 |+ #include <boost/core/span.hpp>
```

> Username: vinniefalco  
> Created_at: 2022-04-21 14:08:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#discussion_r855227219  

`#include <boost/beast/core/detail/config.hpp>` should come first


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-04-21 16:12:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#issuecomment-1105428239  

![pullrequest](https://2411.beast.tracing.cppalliance.org/pullrequest-71d0dfa4.png)  
Timeline tracing charts: [https://2411.beast.tracing.cppalliance.org/index.html](https://2411.beast.tracing.cppalliance.org/index.html)

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-04-21 18:58:00 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#issuecomment-1105641051  

please squash them down to 1 commit

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-04-21 23:12:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#issuecomment-1105844527  

![pullrequest](https://2411.beast.tracing.cppalliance.org/pullrequest-348e32e1.png)  
Timeline tracing charts: [https://2411.beast.tracing.cppalliance.org/index.html](https://2411.beast.tracing.cppalliance.org/index.html)

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-04-21 23:31:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#issuecomment-1105855143  

231 fewer lines, nice :)

---

## Comment 7

> Username: sehe  
> Created_at: 2022-04-22 02:31:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#issuecomment-1105942281  

Is drone test failing on OSX (due to missing openssl) a known issue?

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2022-04-22 03:22:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#issuecomment-1105963819  

@sdarwin OSX?

---

## Comment 9

> Username: sdarwin  
> Created_at: 2022-04-22 13:55:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#issuecomment-1106542236  

It should be fixed now.  The brew package changed.   
Added symlink `ln -s /usr/local/opt/openssl@3 /usr/local/opt/openssl`    
The following text is only for reference, since the symlink solves the problem.  
  
```  
openssl@3 is keg-only, which means it was not symlinked into /usr/local,  
because macOS provides LibreSSL.  
  
If you need to have openssl@3 first in your PATH, run:  
  echo 'export PATH="/usr/local/opt/openssl@3/bin:$PATH"' >> ~/.zshrc  
For compilers to find openssl@3 you may need to set:  
  export LDFLAGS="-L/usr/local/opt/openssl@3/lib"  
  export CPPFLAGS="-I/usr/local/opt/openssl@3/include"  
For pkg-config to find openssl@3 you may need to set:  
  export PKG_CONFIG_PATH="/usr/local/opt/openssl@3/lib/pkgconfig"  
```

---

## Comment 10

> Username: glenfe  
> Created_at: 2022-04-23 01:14:53 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#issuecomment-1107137689  

The only X now is Code coverage: 94.69% (changed -0.01%) .

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2022-04-23 03:45:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2411#issuecomment-1107352091  

I wouldn't worry about that

---
