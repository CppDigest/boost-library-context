# #1065 Algorithm jSO [Merged]

> Username: NAThompson  
> Created at: 2024-01-19 23:50:30 UTC  
> Updated at: 2024-01-22 22:06:56 UTC  
> Merged at: 2024-01-22 22:06:52 UTC  
> Closed at: 2024-01-22 22:06:52 UTC  
> Url: https://github.com/boostorg/math/pull/1065  

Follows: Brest, Janez, Mirjam Sepesy Maucec, and Borko Boskovic. "Single objective real-parameter optimization: Algorithm jSO." 2017 IEEE congress on evolutionary computation (CEC). IEEE, 2017.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2024-01-22 02:11:11 UTC  
> Url: https://github.com/boostorg/math/pull/1065#issuecomment-1902901120  

@mborland , @jzmaddock : Check out the example, I think this came out to be _really_ cool. Obviously it would've been cooler to plot the progress than just spit it out, but still I think this is a fairly unique capability.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2024-01-22 22:06:46 UTC  
> Url: https://github.com/boostorg/math/pull/1065#issuecomment-1904908288  

@jzmaddock , @mborland : The failures are network issues. I confess I don't see much hope for a careful review of this one-it's a bunch of small but tedious improvements on the key idea of differential evolution.  
  
I was hoping to achieve parallelization but it seems that's impossible as nearly every data structure is mutable.

---
