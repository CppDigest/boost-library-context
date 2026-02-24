# #608 - Type convertibility to `Backend` is assumed true if type is convertible to `number<Backend, et>` [Closed]

> Username: oliverlee  
> Created at: 2024-03-06 16:26:58 UTC  
> Updated at: 2024-03-07 17:27:19 UTC  
> Closed at: 2024-03-07 17:23:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/608  

Hard error in default ops where a type `V` is assumed to be assignable to a backend type `B`.   
  
https://godbolt.org/z/ebEj5nzvh

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-03-06 17:00:52 UTC  
> Updated at: 2024-03-06 17:01:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/608#issuecomment-1981358998  

Confirmed, we're not differentiating between `number<>` is constructible from V because:  
  
1) number<> has a suitable constructor, and  
2) V has a conversion operator.  
  
(1) is handled, (2) is not and causes the error.  The easiest fix (to avoid exploding the number of operator overloads), might be to disallow 2) in mixed operations.  This also sort of feels like the right fix to me, what do folks think?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2024-03-06 17:33:32 UTC  
> Updated at: 2024-03-06 17:42:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/608#issuecomment-1981424309  

> Confirmed, we're not differentiating between number<> is constructible from `V`  
  
> The easiest fix (to avoid exploding the number of operator overloads), might be to disallow 2) in mixed operations. This also sort of feels like the right fix to me, what do folks think?  
  
I played around with the overloads and found no simple fix. In the quickness of this issue, yes, a terse fix sounds better. If anything else breaks thereby, I do not know.  
  
This is an interesting case, similar to one that I had in another, unrelated project. There I also chose to disregard the _convertible_ case via basically the same way suggested by @jzmaddock.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2024-03-06 17:37:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/608#issuecomment-1981435551  

This stuff gets confusing when implicitly doing narrowing casts yet constructing works. It is sometimes tough to find exactly the right flavors of overloads. It's not really a help, but I've struggled whith this topic on several occasions.  
  
Let it rip in CI and see, ... also with Math.  
  
Cc: @mborland

---

## Comment 4

> Username: oliverlee  
> Created at: 2024-03-06 17:46:00 UTC  
> Updated at: 2024-03-06 17:46:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/608#issuecomment-1981458229  

I don't know what the implications would be allowing 2. and eagerly performing a conversion and then obtaining the backend. Disallowing 2. sounds reasonable - what would the new constraint would be?  
  
For now, we specialize `is_compatible_arithmetic_type` to be `false`.

---

## Comment 5

> Username: oliverlee  
> Created at: 2024-03-06 17:54:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/608#issuecomment-1981471404  

Also a better example of a wrapper type may be `std::reference_wrapper`

---

## Comment 6

> Username: jzmaddock  
> Created at: 2024-03-07 17:27:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/608#issuecomment-1984057451  

This should now be addressed in develop - by addressed, I mean your example is still a compiler error because no operator overload is found, but it's the correct error now ;)
