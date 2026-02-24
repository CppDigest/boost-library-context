# #171 Order the arguments of pointer_in_range [Open]

> Username: Quuxplusone  
> Created at: 2024-04-17 20:46:44 UTC  
> Updated at: 2024-12-14 12:56:50 UTC  
> Url: https://github.com/boostorg/core/pull/171  

Semantically ordered arguments should be lexically ordered, too.   
See https://quuxplusone.github.io/blog/2021/05/05/its-clamping-time/  
  
I feel _very_ strongly that the arguments to `pointer_in_range` — like the arguments to `rotate` and so on (with the notable, regrettable, exception of `std::clamp`) — should be given in "semantic order." This means that `pointer_in_range(a, b, c)` should be `true` iff `a <= b < c`.  
  
With the current definition, the programmer has to do a lot of extra brainwork (i.e. consult the documentation) to figure out that `pointer_in_range(a, b, c)` is `true` iff `a >= b && a < c`. (As opposed, to, say, if `c >= a && c < b`.) Putting the "middle" argument in the "middle" is simply the most natural and self-explanatory choice.  
  
The changes in the test file should speak for themselves: we see that it's only the "middle" argument that's varying, and that the expected return value is `false` only when the "middle" value incorrectly falls outside the "lower" and "higher" bounds.  
  
I'm sending an email to this effect also to the LEWG reflector, strongly recommending that the same change be made in [P3234R0](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2024/p3234r0.html).  
  
Obviously this will be much harder to do after Boost 1.86 ships, so time is of the essence now.

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-04-17 21:52:10 UTC  
> Url: https://github.com/boostorg/core/pull/171#issuecomment-2062480194  

> I feel _very_ strongly that the arguments to `pointer_in_range` — like the arguments to `rotate` and so on (with the notable, regrettable, exception of `std::clamp`) — should be given in "semantic order." This means that `pointer_in_range(a, b, c)` should be `true` iff `a <= b < c`.  
  
I strongly disagree. It is established convention that iterators denoting a range are passed in pairs of arguments, begin and end. Breaking that pair with another argument will be a constant source of confusion and mistakes.  
  
`pointer_in_range(a, b, c)` meaning `a >= b && a < c` makes sense if you read it as "pointer a in range [b; c)". This, of course is just an interpretation that helps remembering the order of the arguments, but this is also sort of a convention in itself. `std::clamp(a, b, c)` is similarly "clamp a between b and c".

---

## Comment 2

> Username: Lastique  
> Created_at: 2024-04-17 22:02:17 UTC  
> Url: https://github.com/boostorg/core/pull/171#issuecomment-2062509590  

BTW, `std::rotate` is terrible. I can never remember what it does.

---

## Comment 3

> Username: Quuxplusone  
> Created_at: 2024-04-17 22:29:21 UTC  
> Url: https://github.com/boostorg/core/pull/171#issuecomment-2062586726  

> It is established convention that iterators denoting a range are passed in pairs of arguments, begin and end. Breaking that pair with another argument will be a constant source of confusion and mistakes.  
  
I do agree that if the function were actually `pointer_in_range(p, rg)`, _taking a range_, that that would be the right signature. But whenever a function takes three arguments that are supposed to be ordered `a, b, c`, it's _really really good_ to put them lexically in that order.  
  
FWIW, `std::rotate` takes the range to rotate and the "new beginning" of the range: it "pulls" the new beginning leftward so that that element is now at the beginning of the range. Since the "new beginning" argument is always semantically somewhere between `first` and `last`, it is passed _lexically_ between `first` and `last`. `std::rotate(a, b, c)` is defined iff `a <= b <= c`. So, yeah, it's reasonable to get confused whether `std::rotate` is "pulling" the elements leftward or "pushing" them rightward — is the middle argument the new beginning or the new end? — but the one thing you _can't possibly_ be confused about is _what order the arguments are supposed to be in._

---

## Comment 4

> Username: Lastique  
> Created_at: 2024-04-17 22:40:52 UTC  
> Updated_at: 2024-04-17 22:52:58 UTC  
> Url: https://github.com/boostorg/core/pull/171#issuecomment-2062597844  

> it's _really really good_ to put them lexically in that order.  
  
Sorry, I don't see why, aside from personal preference. IMO, the least confusing interface is "good", and the least confusing most of the time means the most consistent with other similar interfaces. "a <= b < c" doesn't exist in C++, so trying to introduce it now, and not even in that literal form but a remote shadow of it in the form of the order of arguments, does not make the interface consistent or any less error-prone at all. It just makes that interface an odd ball to stumble upon.  
  
> So, yeah, it's reasonable to get confused whether `std::rotate` is "pulling" the elements leftward or "pushing" them rightward — is the middle argument the new beginning or the new end? — but the one thing you _can't possibly_ be confused about is _what order the arguments are supposed to be in._  
  
Given that most if not all std interfaces accepting pairs of iterators accept them as consecutive arguments, the pivot iterator in the middle _is_ confusing, at least at first, until you remember that `std::rotate` is "special".

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-04-18 07:46:01 UTC  
> Url: https://github.com/boostorg/core/pull/171#issuecomment-2063236340  

There are at least two reasons to prefer the current argument order. First, in the sentence "the pointer p is in the range [b, e)" the order is p, b, e. (This is consistent with is_base_of.)  
  
Second, the mathematical notation for "p is a member of [b, e)" is p ∈ [b, e).  
  
This is not the same as std::rotate, which takes two consecutive ranges (which also necessarily form a valid range when concatenated.)

---

## Comment 6

> Username: glenfe  
> Created_at: 2024-04-18 09:38:54 UTC  
> Url: https://github.com/boostorg/core/pull/171#issuecomment-2063451276  

Though the current reflector discussion on LEWG about [P3234R0](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2024/p3234r0.html) has focused on `span` range, I'll update the rationale in R1 to discuss this too.

---

## Comment 7

> Username: libbooze  
> Created_at: 2024-11-25 19:44:38 UTC  
> Url: https://github.com/boostorg/core/pull/171#issuecomment-2498890295  

Just stumbled onto this by accident, so apologies for discussing this 7 months after last comment  but I think it would be great if we would have overload that enables you to write:  
```  
if (!pointer_in_range(ptr, {data_, data_ + size_}))  
```  
I think this is a new convention in `std::` so I think WG21 would reject it since it would be very different from rest of `std::`, but maybe in boost we can have that?   
In terms of optimization I am not certain compilers would be able to remove the overhead(although it[ looks fine ](https://godbolt.org/z/TPTvqbe9e)to me), but for sure to me it is much nicer to read.

---

## Comment 8

> Username: glenfe  
> Created_at: 2024-12-14 12:56:49 UTC  
> Url: https://github.com/boostorg/core/pull/171#issuecomment-2543099117  

An overload with span in Core seems OK. I might end up adding that anyway since it looks like the desire is there for [P3234R1](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2024/p3234r1.html) to use that interface too.

---
