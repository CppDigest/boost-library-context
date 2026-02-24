# #39 operator bool() and set_zero(): test for zero and set to zero respectively. [Merged]

> Username: jeremy-murphy  
> Created at: 2016-05-11 08:06:49 UTC  
> Updated at: 2016-05-26 17:40:14 UTC  
> Merged at: 2016-05-26 17:40:14 UTC  
> Closed at: 2016-05-26 17:40:14 UTC  
> Url: https://github.com/boostorg/math/pull/39  

Checking for zero or non-zero and setting to zero are common operations, so fast implementations are useful.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-05-11 10:24:00 UTC  
> Updated_at: 2016-05-15 11:07:32 UTC  
> Url: https://github.com/boostorg/math/pull/39#discussion_r62824483  

This should be explicit when BOOST_NO_CXX11_EXPLICIT_CONVERSION_OPERATORS is not set, otherwise use an implicit conversion to an "unmentionable" type, for example multiprecision::number uses:  
  
   typedef bool (self_type::*unmentionable_type)()const;  
  
   BOOST_MP_FORCEINLINE operator unmentionable_type()const  
   {  
      return is_zero() ? 0 : &self_type::is_zero;  
   }  
  
Which prevents accidental conversion to an arithmetic type.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2016-05-12 11:08:56 UTC  
> Updated_at: 2016-05-15 11:07:32 UTC  
> Url: https://github.com/boostorg/math/pull/39#discussion_r63002315  

Ahh, wow, that's a new one on me. What about naming a function that sets the polynomial to zero? I have used the verb `clear` but maybe `set_zero` is more appropriate? This is on the assumption that it is more efficient than assignment from zero, which I assume the compiler is not quite smart enough to optimize away for a class such as polynomial.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2016-05-12 11:09:32 UTC  
> Url: https://github.com/boostorg/math/pull/39#issuecomment-218727215  

I still need to update the docs after we settle on the names.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2016-05-12 11:10:44 UTC  
> Url: https://github.com/boostorg/math/pull/39#issuecomment-218727448  

And what about the horrible workaround for gcc 4.6.x?

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2016-05-12 11:47:59 UTC  
> Updated_at: 2016-05-15 11:07:32 UTC  
> Url: https://github.com/boostorg/math/pull/39#discussion_r63006046  

On 12/05/2016 12:08, Jeremy W. Murphy wrote:  
  
> In include/boost/math/tools/polynomial.hpp   
> https://github.com/boostorg/math/pull/39#discussion_r63002315:  
>   
> > @@ -455,6 +454,18 @@ class polynomial :  
> >         normalize();  
> >         return *this;  
> >     }  
> > +  
> > -   // Useful for fast test of equality to zero.  
> > -   operator bool() const  
>   
> Ahh, wow, that's a new one on me. What about naming a function that   
> sets the polynomial to zero? I have used the verb |clear| but maybe   
> |set_zero| is more appropriate? This is on the assumption that it is   
> more efficient than assignment from zero, which I assume the compiler   
> is not quite smart enough to optimize away for a class such as polynomial.  
  
clear() works for me.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2016-05-12 11:49:04 UTC  
> Url: https://github.com/boostorg/math/pull/39#issuecomment-218734474  

On 12/05/2016 12:10, Jeremy W. Murphy wrote:  
  
> And what about the horrible workaround for gcc 4.6.x?  
  
I don't believe that's required in this case - it was the presence of   
other operator() overloads that caused the issue (it called the bool   
operator rather than the correct one if memory serves).

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2016-05-23 03:15:32 UTC  
> Updated_at: 2016-05-23 23:14:22 UTC  
> Url: https://github.com/boostorg/math/pull/39#issuecomment-220879351  

John, if you're happy with my choice of `set_zero` as the name for the modifying function, then this is all done from my end.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2016-05-24 11:53:55 UTC  
> Url: https://github.com/boostorg/math/pull/39#issuecomment-221246022  

Can you mark operator bool in the docs as explicit to match the code?  
  
Re the name "set_zero", I confess I'm not overly keen on set_ and get_ prefixes, so I would be inclined to go with either just "zero" or even "clear".  Does assigning from a literal zero produce the same result BTW?  
  
Thanks for this, John.

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2016-05-26 04:20:39 UTC  
> Url: https://github.com/boostorg/math/pull/39#issuecomment-221773877  

I'm definitely not in favour of the object-oriented habit of 'getters and setters' on a per-member basis, but I thought it was OK in this case. I thought "clear" was too tied to the underlying implementation and is not meaningful to a polynomial per se (although that's not a huge sin). "zero" seems ambiguous as to whether it is setting or querying? I don't love "set_zero" but the alternatives all seem more problematic.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2016-05-26 17:40:09 UTC  
> Url: https://github.com/boostorg/math/pull/39#issuecomment-221941767  

OK lets go with what we have and move on, merging to develop...

---
