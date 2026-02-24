# #185 - Consider renaming `if_` and `while_` to `static_if` and `static_while` [Closed]

> Username: ldionne  
> Created at: 2015-10-06 14:00:21 UTC  
> Updated at: 2015-10-20 12:42:04 UTC  
> Closed at: 2015-10-20 12:42:04 UTC  
> Url: https://github.com/boostorg/hana/issues/185  



---

## Comment 1

> Username: vendethiel  
> Created at: 2015-10-06 22:13:30 UTC  
> Url: https://github.com/boostorg/hana/issues/185#issuecomment-146017559  

Why? Could they be dynamic (and still be in hana)?

---

## Comment 2

> Username: ahundt  
> Created at: 2015-10-06 23:03:28 UTC  
> Url: https://github.com/boostorg/hana/issues/185#issuecomment-146029023  

for statements that would be used frequently like those I would lean towards the current names

---

## Comment 3

> Username: ldionne  
> Created at: 2015-10-08 18:16:32 UTC  
> Url: https://github.com/boostorg/hana/issues/185#issuecomment-146643224  

> Why? Could they be dynamic (and still be in hana)?  
  
Currently, `if_` and `while_` can be used with runtime boolean conditions too, so indeed `static_xxx` would be a bad name. However, I'm looking at the possibility of only supporting `integral_constant`-like conditions for a first release, because that would allow simplifying the `Logical` concept, which I don't like all that much.  
  
> for statements that would be used frequently like those I would lean towards the current names  
  
From my experience, those statements are not used all that frequently. `while_` definitely isn't, and `if_` is slightly more common but not that bad. If using `static_if` can add clarity, that should be the main goal. Note that this is motivated by a comment by someone during my CppCon talk; I was explaining code with an `if_` in it, and when a person asked me whether that was _basically a static if_, it made everything much simpler I think.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-10-20 12:42:04 UTC  
> Url: https://github.com/boostorg/hana/issues/185#issuecomment-149552801  

Screw this for now; let's not lose generality by implying that they can only be used with `IntegralConstant`s. There does not seem to be a sufficiently compelling reason to rename them.
