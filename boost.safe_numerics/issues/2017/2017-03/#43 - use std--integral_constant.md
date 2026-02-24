# #43 - use std::integral_constant [Closed]

> Username: akrzemi1  
> Created at: 2017-03-14 17:32:17 UTC  
> Updated at: 2021-05-23 22:26:15 UTC  
> Closed at: 2021-05-23 22:26:15 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/43  

consider using `std::integral_constant` instead of `safe_literal`.

---

## Comment 1

> Username: pdimov  
> Created at: 2018-10-15 19:27:37 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/43#issuecomment-429982657  

+1

---

## Comment 2

> Username: robertramey  
> Created at: 2018-11-24 23:23:39 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/43#issuecomment-441403192  

I've thought about this some more and I think I remember why I don't have this.  
  
The current safe_literal has the same type signature as safe<int> etc.  SO it works in all the overloads for all the operations.  To be able to do safe<int> + integral_constant<int, 42>() I would have to create a new overload for each operation safe operation.  Which I'm reluctant to do as things are already pretty complicated.  I'll consider it though.  Now that it's "all done" adding another little doodad on each overload might be OK.  In addition, right now I overload on is_integer, etc.  As you've pointed out I should really be doing on my concept of Numeric.  If I do that and integral_constant models it then it would work.  I'm still thinking about this.
