# #1209 - Review 2: Promotion section badly named [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 18:53:45 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-14 13:31:12 UTC  
> Url: https://github.com/boostorg/decimal/issues/1209  

>Promotion has a very specific meaning in the C++ language.  What we see here is  
**not** that C++ kind-of promotion.  It simply is a converting constructor.  
Please consider renaming this section.  
  
>The example does not explicitly state if this conversion is implicit or  
explicit (not pun intended).  Please consider stating if this is an `explicit`  
constructor or not.  
  
>The example does not show what happens if the integer cannot be represented  
precisely by the DFP type.  `decimal32_t` is not able to precisely represent  
all 32 bit `int` values, and AFAIK 64 bit `int` is allowed (not just by the  
language but ABIs as well) so we may even have this issue with `decimal64_t`.  
The question we should answer is: how will rounding be done and what cohort  
will be used to represent the precision after the conversion.  
  
>The text added before the example code should state if the connversion is  
`explicit` or not, describe the rounding used and the what cohort is created.  
  
Comment: I think the reviewer has partly misread the example (more hand holding!)  There is both a converting constructor in use, and promotion when performing mixed arithmetic.  
  
So... I would tend to add a short new section on the converting constructors - which answers the questions above, plus what happens when converting from binary floats (and the problems that has).  Then this section needs to be clearer that it is about mixed mode arithmetic (and perhaps should be renamed as such).  But then it needs to state which types can be mixed - I might assume that any type that has an implicit conversion to the larger type - but I haven't checked that.  What about ints?  Can I always multiply by a literal integer for example?

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-06 08:03:17 UTC  
> Url: https://github.com/boostorg/decimal/issues/1209#issuecomment-3495700128  

Construction from integers is now `explicit`, but all operation between a decimal and an integer are defined e.g. `decimal32_t * uint32_t`. Mixed decimal arithmetic always promotes to the higher precision types e.g. `decimal32_t * decimal64_t -> decimal64_t`. Implicit mixed decimal construction is allowed for increase in precision, and explicit for decrease in precision.
