# #163 - Is basic_parser::max_depth() == 32 the right default: [Closed]

> Username: vinniefalco  
> Created at: 2020-08-17 17:30:48 UTC  
> Updated at: 2020-09-01 15:02:14 UTC  
> Closed at: 2020-09-01 15:02:14 UTC  
> Url: https://github.com/boostorg/json/issues/163  

This affects the `parse` free functions, since users cannot control the parser it uses

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-18 01:29:15 UTC  
> Url: https://github.com/boostorg/json/issues/163#issuecomment-675197607  

How was the number chosen in the first place?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-18 01:54:06 UTC  
> Url: https://github.com/boostorg/json/issues/163#issuecomment-675203784  

16 seemed too small and 64 seemed too large :) but seriously...I"m not sure, maybe it came from the spec as a suggestion.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-18 02:43:49 UTC  
> Url: https://github.com/boostorg/json/issues/163#issuecomment-675216274  

Check this out:  
https://softwareengineering.stackexchange.com/questions/279207/how-deeply-can-a-json-object-be-nested

---

## Comment 4

> Username: sdkrystian  
> Created at: 2020-08-18 03:13:27 UTC  
> Url: https://github.com/boostorg/json/issues/163#issuecomment-675223936  

I feel that we can go deeper still; our parse functions don't use all that much stack space (or we might be worse than nlohmann as we use SSE2)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-08-18 03:22:21 UTC  
> Url: https://github.com/boostorg/json/issues/163#issuecomment-675226374  

The purpose of having a small default depth is to be "secure by default." The number 32 isn't set in stone though, we can consult with Bishop Fox to see if this number should be changed.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-09-01 15:02:14 UTC  
> Url: https://github.com/boostorg/json/issues/163#issuecomment-684920191  

I think 32 is gud. If users complain we can change it later.
