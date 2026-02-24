# #1001 Use Dijkstra identities for 0(0th) Fibonacci convention [Merged]

> Username: ryanelandt  
> Created at: 2023-07-24 17:00:10 UTC  
> Updated at: 2023-07-25 17:58:51 UTC  
> Merged at: 2023-07-25 16:55:24 UTC  
> Closed at: 2023-07-25 16:55:24 UTC  
> Url: https://github.com/boostorg/math/pull/1001  

Dijkstra's note "In honour of Fibonacci" provides identities for the convention where 0 is the 1st Fibonacci number. Boost Math uses the convention that 0 is the 0th Fibonacci number. This PR updates these identities to follow the 0 is the 0th convention.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2023-07-24 23:25:28 UTC  
> Url: https://github.com/boostorg/math/pull/1001#issuecomment-1648753963  

But this is merely convention no?

---

## Comment 2

> Username: ryanelandt  
> Created_at: 2023-07-25 03:23:34 UTC  
> Url: https://github.com/boostorg/math/pull/1001#issuecomment-1648994689  

The identity depends on the convention.  
  
Using the 0(0th) convention the first formula is:   F(2n-1) = F(n-1)^2 + F(n)^2  
Using the 0(1st) convention the first formula is:   F(2n)   = F(n)^2 + F(n+1)^2  
  
To see why, let's assign a letter to the Fibonacci numbers as follows: a=0, b=1, c=1, d=2, e=3, f=5, g=8, h=13, ... . Now consider the formula:  
d^2 + e^2 = h  
  
Using the **0(0th)** convention d=3rd, e=4th, h=7th, so the **0(0th)** formula is **right**, but the 0(1st) formula is wrong.  
Using the **0(1st)** convention d=4th, e=5th, h=8th, so the 0(0th) formula is wrong, but the **0(1st)** formula is **right**.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2023-07-25 15:19:52 UTC  
> Url: https://github.com/boostorg/math/pull/1001#issuecomment-1650045900  

But this only changes a comment, so the code is ostensibly still correct?

---

## Comment 4

> Username: ryanelandt  
> Created_at: 2023-07-25 16:24:43 UTC  
> Updated_at: 2023-07-25 16:27:25 UTC  
> Url: https://github.com/boostorg/math/pull/1001#issuecomment-1650155982  

The code is correct. But, for comments to be sensible, they need to explain what the code is doing. The original comments do not do this. Consider an analogous situation...  
  
The commented function below converts temperature:  
```  
double kelvin_to_rankine(double temp_in) {  
  // Uses the formula temp_out = 9/5 * temp_in + 32  
  return temp_in * 1.8;  
}  
```  
The comment explaining what this function does is not sensible because the formula is wrong, it's the formula for something else. Instead the commented function should read:  
```  
double kelvin_to_rankine(double temp_in) {  
  // Uses the formula temp_out = 9/5 * temp_in as both kelvin and rankine are zero at absolute zero  
  return temp_in * 1.8;  
}  
```  
In the Fibonacci case here, the formulas in the comments need to match the convention being used otherwise they are confusing as they return incorrect outputs.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2023-07-25 16:55:47 UTC  
> Url: https://github.com/boostorg/math/pull/1001#issuecomment-1650199644  

@ryanelandt : You've convinced me-merged.  
  
Build failure is a timeout.

---

## Comment 6

> Username: ryanelandt  
> Created_at: 2023-07-25 17:58:46 UTC  
> Url: https://github.com/boostorg/math/pull/1001#issuecomment-1650293744  

Awesome, thanks.

---
