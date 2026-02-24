# #2561 Drone apple m1 [Closed]

> Username: sdarwin  
> Created at: 2022-11-08 15:34:23 UTC  
> Updated at: 2023-01-16 14:22:36 UTC  
> Closed at: 2023-01-16 14:22:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2561  

SSL errors: "building for macOS-x86_64 but attempting to link with file built for macOS-arm64"

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2022-12-20 06:57:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2561#issuecomment-1358916139  

```cpp  
ld: warning: ignoring file /usr/local/opt/openssl/lib/libcrypto.dylib, building for macOS-x86_64 but attempting to link with file built for macOS-arm64  
ld: warning: ignoring file /usr/local/opt/openssl/lib/libssl.dylib, building for macOS-x86_64 but attempting to link with file built for macOS-arm64  
```

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2022-12-20 06:59:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2561#issuecomment-1358917456  

Wrong version, are we cross compiling?

---

## Comment 3

> Username: sdarwin  
> Created_at: 2023-01-16 14:22:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2561#issuecomment-1384140504  

Fixed in PR 2622

---
