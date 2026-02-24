# #250 Documentation/operators [Closed]

> Username: ricejasonf  
> Created at: 2016-02-03 05:48:00 UTC  
> Updated at: 2016-02-05 23:21:45 UTC  
> Closed at: 2016-02-05 23:21:45 UTC  
> Url: https://github.com/boostorg/hana/pull/250  

There are two things to note here:  
1. I took the liberty of removing documentation of a pointer operand for `integral_constant` because I think it no longer exists. It is in a separate commit just in case.  
2. For the data-types that I added a documentation definition to, for some reason the "Public Members" documentation is not full width, and I could not find any reason just by looking at the style rules. I could possibly add a style rule unless I am missing something obvious with this.

---

## Comment 1

> Username: ldionne  
> Created_at: 2016-02-04 20:38:27 UTC  
> Updated_at: 2016-02-05 19:20:48 UTC  
> Url: https://github.com/boostorg/hana/pull/250#discussion_r51933707  

This should probably be made a `friend`, otherwise it does not make sense to receive two arguments. The same applies for some operators in the other files you modified.

---

## Comment 2

> Username: ricejasonf  
> Created_at: 2016-02-05 04:27:14 UTC  
> Url: https://github.com/boostorg/hana/pull/250#issuecomment-180188271  

I just broke all of these free functions out into a separate file and then realized that maybe you meant that I simply add the friend keyword there. Which is preferred?

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2016-02-05 04:44:10 UTC  
> Url: https://github.com/boostorg/hana/pull/250#issuecomment-180195423  

I perhaps went too far with breaking out the free operators, but I pushed it for review to see if this is a better solution. If not, I can reset back to what I had before and work from there. lmk

---

## Comment 4

> Username: ldionne  
> Created_at: 2016-02-05 13:15:34 UTC  
> Url: https://github.com/boostorg/hana/pull/250#issuecomment-180352611  

I really just meant that you should stick `friend` in front of the functions :p.

---

## Comment 5

> Username: ricejasonf  
> Created_at: 2016-02-05 19:36:01 UTC  
> Url: https://github.com/boostorg/hana/pull/250#issuecomment-180515610  

I reset back to earlier and simply added `friend` to all free operators. I also added `type`'s unary+ and everything for `integral_constant`.

---

## Comment 6

> Username: ldionne  
> Created_at: 2016-02-05 21:15:52 UTC  
> Url: https://github.com/boostorg/hana/pull/250#issuecomment-180556190  

That looks perfect. I will merge as soon as the CI is finished. Thanks a lot!

---

## Comment 7

> Username: ricejasonf  
> Created_at: 2016-02-05 22:00:42 UTC  
> Url: https://github.com/boostorg/hana/pull/250#issuecomment-180584115  

No sweat! :sweat_smile:

---

## Comment 8

> Username: ldionne  
> Created_at: 2016-02-05 23:21:45 UTC  
> Url: https://github.com/boostorg/hana/pull/250#issuecomment-180618145  

Closed by bae4dc1d8dec14a17e6c8c82c436e723dc7a4514.

---
