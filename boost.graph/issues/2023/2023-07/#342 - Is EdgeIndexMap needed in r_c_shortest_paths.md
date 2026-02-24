# #342 - Is EdgeIndexMap needed in r_c_shortest_paths ? [Open]

> Username: andrea-cassioli-maersk  
> Created at: 2023-07-25 06:49:05 UTC  
> Updated at: 2026-01-04 21:04:37 UTC  
> Url: https://github.com/boostorg/graph/issues/342  

I have been using `r_c_shortest_paths` for a while, and recently because of a code refactoring I realised that one of the template parameters, namely `EdgeIndexMap`, seems not to be used anywhere in `r_c_shortest_paths`. I was then wonder whether I am missing its purpose. Could anyone shed a light?

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2023-07-28 01:22:29 UTC  
> Url: https://github.com/boostorg/graph/issues/342#issuecomment-1654837387  

Interesting. I would have to dig into the commit history to find out why.

---

## Comment 2

> Username: andrea-cassioli-maersk  
> Created at: 2023-09-13 08:46:33 UTC  
> Url: https://github.com/boostorg/graph/issues/342#issuecomment-1717207180  

Hey @jeremy-murphy , any luck finding more about this?

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2023-09-20 05:06:57 UTC  
> Url: https://github.com/boostorg/graph/issues/342#issuecomment-1726977263  

Sorry, I haven't had a chance to work on it.

---

## Comment 4

> Username: andrea-cassioli-maersk  
> Created at: 2025-04-07 11:02:02 UTC  
> Url: https://github.com/boostorg/graph/issues/342#issuecomment-2782934034  

So I realised this is hanging and I have just tried removing the EdgeIndexMap from all the version of rcsp in version `1.86.0` and seems to work. As far as I can see there is no use whatsoever.  
  
In the first version I can find  
  
https://github.com/boostorg/graph/commit/bb1977b17abcbe39c5a79fefbeed608781de679b#diff-bf4147b63ecc94907ce57f721126a699ae6f8696efbe83ea87dfdadc3706ab0f  
  
there is no use for the edge map anywhere.

---

## Comment 5

> Username: andreacassioli  
> Created at: 2025-12-12 20:11:17 UTC  
> Url: https://github.com/boostorg/graph/issues/342#issuecomment-3647988708  

I gave it another look. I quite sure it is not use. In the very first commit the parameter did have a variable name also in the dispatch, but still the edge map was unused.  
  
I guess for sake of compatibility it is not a question of removing it, but maybe a note in the docs?

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2025-12-29 00:56:18 UTC  
> Url: https://github.com/boostorg/graph/issues/342#issuecomment-3695200619  

I would prefer to fix it completely, even if it means a breaking change.

---

## Comment 7

> Username: andrea-cassioli-maersk  
> Created at: 2026-01-04 16:57:36 UTC  
> Url: https://github.com/boostorg/graph/issues/342#issuecomment-3708248215  

> I would prefer to fix it completely, even if it means a breaking change.  
  
I will give it a shot. Do you mind a bit of code upgrade to c++14 (that the current minimum standard, right?)

---

## Comment 8

> Username: jeremy-murphy  
> Created at: 2026-01-04 21:04:37 UTC  
> Url: https://github.com/boostorg/graph/issues/342#issuecomment-3708424584  

Please go ahead.
