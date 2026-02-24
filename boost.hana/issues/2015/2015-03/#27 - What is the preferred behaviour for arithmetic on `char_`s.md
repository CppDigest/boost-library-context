# #27 - What is the preferred behaviour for arithmetic on `char_`s? [Closed]

> Username: ldionne  
> Created at: 2015-03-22 16:51:13 UTC  
> Updated at: 2015-05-28 23:38:51 UTC  
> Closed at: 2015-05-28 23:38:51 UTC  
> Url: https://github.com/boostorg/hana/issues/27  

Do we want `char_<1> + char_<2> == char_<3>` or `char_<1> + char_<2> == int_<3>`? I think I remember that @jfalcou had raised this issue at C++Now last year, but we never got around to discussing it.

---

## Comment 1

> Username: cheinigk  
> Created at: 2015-03-24 10:03:43 UTC  
> Url: https://github.com/boostorg/hana/issues/27#issuecomment-85430577  

IMHO, for a user who starts using the library it would be easier to understand the meaning of `char_<1> + char_<2> == char_<3>`.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-03-24 20:46:13 UTC  
> Url: https://github.com/boostorg/hana/issues/27#issuecomment-85685452  

@jfalcou, do you remember why you initially suggested we might want to make it `int_<3>`? Or maybe I misremember and you were suggesting it should be `char_<3>`? Anyway, the current behaviour is `char_<3>`.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-03-24 20:47:53 UTC  
> Url: https://github.com/boostorg/hana/issues/27#issuecomment-85686001  

I personally do not really have an opinion about this, since I fail to see why one would want to add two `char_`s. It might be useful, I just don't see how. Is there a use case for adding `char_`s?

---

## Comment 4

> Username: jfalcou  
> Created at: 2015-03-24 20:51:52 UTC  
> Updated at: 2015-03-24 20:52:57 UTC  
> Url: https://github.com/boostorg/hana/issues/27#issuecomment-85686857  

forget char_, think at the level of integral_constant.  
  
what the behavior of integral_constant<T,N> + integral_constant<T,M> be ?  
- `integral_contant<T,N+M>` with risk of overflow/roll over but a trivial and intuitive coding  
- `integral_contant<decltype(T()+T()),N+M>` which carry out C standard integral promotion but changes types of the constant which maybe surprising  
  
My initial remarks was that in fact, both use cases may have their merit and applicability. MPL usually do the first but the second may help write simpler meta-code.

---

## Comment 5

> Username: ldionne  
> Created at: 2015-03-24 21:08:42 UTC  
> Url: https://github.com/boostorg/hana/issues/27#issuecomment-85691013  

Right now, Hana uses the first one. @chrbau2711 seems to think this is right, you suggest doing otherwise might lead to simpler code. If we want to have a Monoid, adding two `integral_constant<T, v>`s has to return an `integral_constant<T, ...>`. I think conserving the type is more intuitive, but at the same time it isn't consistent with the language since `char + char = int`. I'll leave this open, but I'll leave things as-is unless there is a clear motivation to do otherwise.

---

## Comment 6

> Username: ldionne  
> Created at: 2015-05-28 23:38:51 UTC  
> Url: https://github.com/boostorg/hana/issues/27#issuecomment-106629807  

I'll close this to help clear up the issues list and I'll reopen if the issue comes up again.
