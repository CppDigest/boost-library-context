# #15 Add < and ==, making polynomial an ordered ring. [Closed]

> Username: jeremy-murphy  
> Created at: 2015-10-25 13:51:42 UTC  
> Updated at: 2026-02-19 02:14:22 UTC  
> Closed at: 2015-10-31 02:58:32 UTC  
> Url: https://github.com/boostorg/math/pull/15  

Hey, just wondering if there is interest in improving the `polynomial` class?  
  
I have a few things in mind but ordered ring operators were just an easy thing to start off with.  
  
Cheers.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-10-29 10:33:47 UTC  
> Url: https://github.com/boostorg/math/pull/15#issuecomment-152139425  

I don't think polynomials are really ordered?

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2015-10-29 13:52:08 UTC  
> Url: https://github.com/boostorg/math/pull/15#issuecomment-152185280  

You're right, they don't have a natural total ordering like integers do, it's just a default total ordering for practical convenience. Originally I was just going to add equality, which is necessary for GCD, but then I gave in to the temptation of adding more since it seemed harmless.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2015-10-29 14:00:30 UTC  
> Url: https://github.com/boostorg/math/pull/15#issuecomment-152189707  

An argument in favour of granting a default total ordering is that it is required, strictly speaking, for a type to be _regular_ by the definition in Elements of Programming, and thus interoperable with all the facilities of the STL. (E.g. it permits sort and binary search.)

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2015-10-29 23:55:31 UTC  
> Url: https://github.com/boostorg/math/pull/15#issuecomment-152360027  

However, total ordering is not necessary for GCD, so if it is too contentious I am happy to take it out. Equality is the essential thing.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2015-10-30 17:14:45 UTC  
> Url: https://github.com/boostorg/math/pull/15#issuecomment-152590888  

I think making this play nice with std lib containers is a good argument in favour of these.  
  
However,  
- If this class is to get a major workover, then I'd rather make all the changes (or at least most of them) at once, and:  
- I suspect these operators sort in the "wrong" order (so far as there is one) in that they sort on the x^0 coefficient first and the x^n coefficient last.

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2015-10-31 02:58:32 UTC  
> Url: https://github.com/boostorg/math/pull/15#issuecomment-152688631  

OK, sure, that makes sense. I was just going on the usual assumption that smaller pull requests are easier to review but in this case it's preferable to see the whole impact in one go.   
  
Ah, you're right, of course. I agree there is no right or wrong order, only a convention. So the correct definition of `operator<` would be to swap the arguments, `return b.data() < a.data()`, right? Anyway, I'm going to leave ordering out for now because it is distracting from the real purpose of GCD.  
  
I'll close this PR and come back soon with a complete one for Euclidean GCD, but I'm inclined to leave Stein GCD to a separate PR. Is that OK?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2015-10-31 11:34:06 UTC  
> Url: https://github.com/boostorg/math/pull/15#issuecomment-152725992  

On 31/10/2015 02:58, Jeremy W. Murphy wrote:  
  
> OK, sure, that makes sense. I was just going on the usual assumption   
> that smaller pull requests are easier to review but in this case it's   
> preferable to see the whole impact in one go.  
>   
> Ah, you're right, of course. I agree there is no right or wrong order,   
> only a convention. So the correct definition of |operator<| would be   
> to swap the arguments, |return b.data() < a.data()|, right? Anyway,   
> I'm going to leave ordering out for now because it is distracting from   
> the real purpose of GCD.  
>   
> I'll close this PR and come back soon with a complete one for   
> Euclidean GCD, but I'm inclined to leave Stein GCD to a separate PR.   
> Is that OK?  
>   
> OK that sounds fine, Best John.

---
