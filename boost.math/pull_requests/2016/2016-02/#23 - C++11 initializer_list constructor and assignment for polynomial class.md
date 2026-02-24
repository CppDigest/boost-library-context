# #23 C++11 initializer_list constructor and assignment for polynomial class [Merged]

> Username: jeremy-murphy  
> Created at: 2016-02-09 10:56:11 UTC  
> Updated at: 2016-03-04 00:48:59 UTC  
> Merged at: 2016-03-02 19:38:00 UTC  
> Closed at: 2016-03-02 19:38:00 UTC  
> Url: https://github.com/boostorg/math/pull/23  

A constructor and copy assignment operator from `initializer_list`.  
Semantics are copied from `vector` in libstdc++.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-03-02 12:02:22 UTC  
> Url: https://github.com/boostorg/math/pull/23#issuecomment-191211906  

I made a brief example of initializer_list construction, so I think this one is ready to go.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-03-02 17:54:43 UTC  
> Url: https://github.com/boostorg/math/pull/23#issuecomment-191349370  

Thanks for this!  
  
Can we please change the #if logic to use the appropriate Boost.Config macros?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2016-03-02 19:42:20 UTC  
> Url: https://github.com/boostorg/math/pull/23#issuecomment-191393233  

Never mind, I merged it locally and made the changes.  
Also changed the call to pow with integer arguments to use a trivial integer-power routine - fixes a build failure on VC10, but also loss of digits when T is a built in type with more digits than double (ie long long).  
  
It's a pretty brain dead power function and there's no error handling - any exponent in double figures will likely cause numeric overflow :(  Note sure what we do about that...

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2016-03-02 20:11:30 UTC  
> Url: https://github.com/boostorg/math/pull/23#issuecomment-191405942  

Ah, sorry, I wasn't aware of those particular Boost macros. Next time.  
  
I'm fairly sure that there are integer power algorithms in Knuth and at least for 32-bit integers in Hacker's Delight, so I should be able to come up with something. Begs the question of where such an algorithm should go in Boost in general.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2016-03-03 10:56:48 UTC  
> Url: https://github.com/boostorg/math/pull/23#issuecomment-191706128  

> I'm fairly sure that there are integer power algorithms in Knuth and   
> at least for 32-bit integers in Hacker's Delight, so I should be able   
> to come up with something. Begs the question of where such an   
> algorithm should go in Boost in general.  
  
Good question, I guess we haven't provided this before because:  
- For floating point bases, use of std::pow is just as quick typically.  
- For built-in integer types as bases, overflow occurs so easily, that   
  it's questionable whether these are viable general purpose algorithms?  
  
Either way, the version I posted could use having the tail recursion   
removed at the very least...

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2016-03-04 00:48:59 UTC  
> Url: https://github.com/boostorg/math/pull/23#issuecomment-192038839  

Right, yeah, it's a curious problem in that std::pow is satisfactory for most cases, but the fact that it's wrong for some cases does create the need at least for a correct algorithm. I didn't see anything in Knuth specific to integral bases but I only looked quickly. HD has this function, which is more or less an iterative version:  
  
``` C  
int iexp(int x, unsigned n)  
{  
    int p = x, y = 1;  
    while (true)  
    {  
        if (n & 1) y *= p;  
        n >>= 1;  
        if (n == 0) return y;  
        p *= p;  
    }  
}  
```  
  
We can of course play with the types.

---
