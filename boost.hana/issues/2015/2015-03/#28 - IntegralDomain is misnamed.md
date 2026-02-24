# #28 - IntegralDomain is misnamed [Closed]

> Username: ldionne  
> Created at: 2015-03-22 17:02:09 UTC  
> Updated at: 2015-10-08 20:26:46 UTC  
> Closed at: 2015-10-08 20:26:46 UTC  
> Url: https://github.com/boostorg/hana/issues/28  

IntegralDomain is the wrong name for that concept. In abstract algebra, an IntegralDomain is not enough to provide `quot` and `rem`. Technically, I think we need an [Euclidean Domain](http://en.wikipedia.org/wiki/Euclidean_domain).

---

## Comment 1

> Username: m-j-w  
> Created at: 2015-09-13 11:08:47 UTC  
> Url: https://github.com/boostorg/hana/issues/28#issuecomment-139860669  

My suggestion would be an Euclidean Ring which would imply a quotient, a remainder, and an absolute value according to [Euclidean Ring on Mathworld](http://mathworld.wolfram.com/EuclideanRing.html). The latter is not yet available as a method or member of a concept.  
That name also gives rise to the association of the Ring concept, indicating the similarity of multiplication, division with quotient and remainder.

---

## Comment 2

> Username: m-j-w  
> Created at: 2015-09-13 11:37:20 UTC  
> Url: https://github.com/boostorg/hana/issues/28#issuecomment-139862724  

Also, and in relation to the recent discussion about `unique`, shouldn't `quot` and `rem` be `divides` and `modulus`, respectively `div` and `mod` for consistency ? There's a `std::divides` and `std::modulus`, as well as a `mpl::divides` and `mpl::modulus`. Similarly, `operator/` and `operator%` are usually referred to as division and modulus. Sorry for being pedantic about that...

---

## Comment 3

> Username: ldionne  
> Created at: 2015-09-14 14:55:55 UTC  
> Url: https://github.com/boostorg/hana/issues/28#issuecomment-140106438  

Yes, I think we're looking at an Euclidean Ring (which is an Euclidean Domain). I would need to add the `norm` method, and the euclidean algorithm would be defined by the two functions `quot` and `rem`.  
  
Regarding the name of `quot` and `rem`: There's a subtle difference between `div`/`mod` and `quot`/`rem` in Haskell. `quot` truncates towards zero, whereas `div` rounds towards minus infinity IIRC. Since what we're doing in C++ is truncate towards zero, I went with that name. This is the historical reason, but you are right that the correct name we should be using is `div`/`mod` for consistency with the standard library. I'll create an issue.
