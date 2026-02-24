# #341 - Fast and lean pow10 [Closed]

> Username: vinniefalco  
> Created at: 2020-09-15 14:08:34 UTC  
> Updated at: 2021-04-26 06:33:38 UTC  
> Closed at: 2021-04-26 06:33:38 UTC  
> Url: https://github.com/boostorg/json/issues/341  

We need a replacement for the big lookup table for performing `double pow10( int )` which is reasonably fast and doesn't take up 5Kb.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-15 16:08:31 UTC  
> Url: https://github.com/boostorg/json/issues/341#issuecomment-692817363  

maybe helpful:   
http://szhorvat.net/pelican/fast-computation-of-powers.html

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-15 16:20:31 UTC  
> Url: https://github.com/boostorg/json/issues/341#issuecomment-692824161  

> http://szhorvat.net/pelican/fast-computation-of-powers.html  
  
Doesn't load for me

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-15 16:22:21 UTC  
> Url: https://github.com/boostorg/json/issues/341#issuecomment-692825137  

We want IEEE `pow10` which means `double`

---

## Comment 4

> Username: grisumbras  
> Created at: 2021-02-25 05:52:08 UTC  
> Url: https://github.com/boostorg/json/issues/341#issuecomment-785632580  

Is this a duplicate of #353 ?

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-02-25 10:33:24 UTC  
> Url: https://github.com/boostorg/json/issues/341#issuecomment-785793056  

FYI, I tried halving the table and calculating powers of 10 via `auto x = pow10(e/2); return x * x * pow10(e%2);`  
Locally it decreased speed of parsing `numbers.json` by 5-6%, but more importantly a lot of tests started failing, because parsing accuracy has decreased due to extra multiplications (3 ULP difference with `strtod`).  
  
```  
# develop  
479K libs/json/build/gcc-10/release/link-static/visibility-hidden/libboost_json.a  
530K libs/json/bench/gcc-10/release/link-static/bench  
Parse bench/data/canada.json,gcc x64/sse2,boost,201  
Parse bench/data/numbers.json,gcc x64/sse2,boost,374  
  
# PR #505  
476K libs/json/build/gcc-10/release/link-static/visibility-hidden/libboost_json.a  
530K libs/json/bench/gcc-10/release/link-static/bench  
Parse bench/data/canada.json,gcc x64/sse2,boost,194 (-3.5%)  
Parse bench/data/numbers.json,gcc x64/sse2,boost,378 (+1%)  
  
# PR #505 + additional halving of exponents  
476K libs/json/build/gcc-10/release/link-static/visibility-hidden/libboost_json.a  
526K libs/json/bench/gcc-10/release/link-static/bench  
Parse bench/data/canada.json,gcc x64/sse2,boost,197 (-1.9%)  
Parse bench/data/numbers.json,gcc x64/sse2,boost,355 (-5%)  
```

---

## Comment 6

> Username: grisumbras  
> Created at: 2021-04-26 06:21:24 UTC  
> Url: https://github.com/boostorg/json/issues/341#issuecomment-826540100  

I think, this should be closed. The approach we use currently results in higher precision.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-04-26 06:28:30 UTC  
> Url: https://github.com/boostorg/json/issues/341#issuecomment-826543833  

Close it
