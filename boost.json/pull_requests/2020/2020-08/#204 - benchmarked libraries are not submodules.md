# #204 benchmarked libraries are not submodules [Closed]

> Username: sdkrystian  
> Created at: 2020-08-24 22:01:01 UTC  
> Updated at: 2020-08-25 02:52:15 UTC  
> Closed at: 2020-08-25 02:52:15 UTC  
> Url: https://github.com/boostorg/json/pull/204  

fix #70

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-08-24 22:05:11 UTC  
> Url: https://github.com/boostorg/json/pull/204#issuecomment-679390175  

What, you don't want to ship copies of RapidJSON and nlohmann's JSON with Boost?

---

## Comment 2

> Username: sdkrystian  
> Created_at: 2020-08-24 22:09:53 UTC  
> Url: https://github.com/boostorg/json/pull/204#issuecomment-679391911  

LOL

---

## Comment 3

> Username: sdkrystian  
> Created_at: 2020-08-24 22:11:04 UTC  
> Url: https://github.com/boostorg/json/pull/204#issuecomment-679392363  

@vinniefalco Should we add `/bench/lib` to the gitignore?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-08-24 22:43:22 UTC  
> Url: https://github.com/boostorg/json/pull/204#issuecomment-679403259  

Yeah we should add it to gitignore if running the script would change the working set

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2020-08-25 02:11:05 UTC  
> Url: https://github.com/boostorg/json/pull/204#issuecomment-679460419  

I added it to .gitignore

---
