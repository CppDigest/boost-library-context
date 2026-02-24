# #77 - sub-container assignment [Closed]

> Username: vinniefalco  
> Created at: 2021-11-01 16:20:45 UTC  
> Updated at: 2022-02-25 00:46:38 UTC  
> Closed at: 2022-02-25 00:46:38 UTC  
> Url: https://github.com/boostorg/url/issues/77  

e.g. `u1.params() = u2.params()`  
  
We might be able to do segments generically.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-02-22 06:12:07 UTC  
> Updated at: 2022-02-22 06:12:34 UTC  
> Url: https://github.com/boostorg/url/issues/77#issuecomment-1047459285  

Does this already work? See if it works already. If not, maybe we could just close the issue.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-02-23 17:15:22 UTC  
> Url: https://github.com/boostorg/url/issues/77#issuecomment-1049015405  

Let me check

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-02-23 17:30:17 UTC  
> Url: https://github.com/boostorg/url/issues/77#issuecomment-1049030305  

It doesn't work yet :(

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-02-24 02:06:25 UTC  
> Url: https://github.com/boostorg/url/issues/77#issuecomment-1049421885  

No custom `operator=` is defined and `assign(other.begin(), other.end())` doesn't work because `*other.begin()` is a different type.  
  
The default `operator=` will just copy the underlying pointer to the URL, which I think is expected behavior?  
  
We can't delete `operator=` because `params` is a reference-like type. So this is going to be the final behavior.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-02-24 05:52:44 UTC  
> Url: https://github.com/boostorg/url/issues/77#issuecomment-1049518495  

can we put `&` or `&&` on `operator=` to prevent this from compiling:  
```  
u1.params() = u2.params();  
```  
  
But still allow this to compile:  
```  
auto p = u1.params();  
p = u2.params();  
```  
?

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-02-24 13:59:43 UTC  
> Url: https://github.com/boostorg/url/issues/77#issuecomment-1049887765  

A single `&` works: https://godbolt.org/z/xxbbEMMx1
